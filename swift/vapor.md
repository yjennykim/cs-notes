# Routing
### Register 
route a request based on HTTP method & path

`...hello/vapor` is registered by 
```swift
// responds to GET /hello/vapor
app.get("hello", "vapor") { req in 
    return "Hello"
}
```

```swift
// responds to GET /hello/vapor
app.on(.GET, "hello", "vapor") {...}
```


### Parameters
```swift
// responds to GET /number/42
// responds to GET /number/1337
// ...
app.get("number", ":x") { req -> String in 
    guard let int = req.parameters.get("x", as: Int.self) else {
        throw Abort(.badRequest)
    }
    return "\(int) is a great number"
}
```

`req.parameters.get` can also cast 

`:name` indicates name is a dynamic component. Access the dynamic value using `req.parameters`

<b>other path components</b>
* Constant (`hello`)
* Parameter (`:name`) - string following `:` is used as parameter's name which can be fetched 
* Anything (`*`) - similar to parameter, except the value is discarded
* Catchall (`**`) - matches one or more components. Any string at this position or later positions will be matched 
```swift
// responds to GET /hello/foo
// responds to GET /hello/foo/bar
// ...
app.get("hello", "**") { req -> String in
    let name = req.parameters.getCatchall().joined(separator: " ")
    return "Hello, \(name)!"
}
```
### Methods
* `get`
* `post`
* `patch`
* `put`
* `delete`

### Route Groups
Route groups groups routes with common path prefix or specific middleware

<b>Group by path</b>
```swift
let users = app.grouped("users")
users.get { req in
    ...
}
users.post { req in
    ...
}
```
alternatively...
```swift
app.group("users") { users in
    users.get { req in
        ...
    }
    users.post { req in
        ...
    }
    users.group(":id") { user in
        // GET /users/:id
        user.get { ... }
        // PATCH /users/:id
        user.patch { ... }
        // PUT /users/:id
        user.put { ... }
    }
}
```
<b>Group by middleware</b>
```swift
let auth = app.grouped(AuthMiddleware())
auth.get("dashboard") { ... }
auth.get("logout") { ... }
```
useful to protect routes with authentication

### Redirections
```swift
req.redirect(to: "/some/new/path")
```
specify type of redirect `redirectType`:
* `permanent`: 301 Permanent
* `.normal`: 303 see other (default)
* `.temporary`: 307 temporary 

# Controllers
collection of methods that take in a request `Request` and returns a response `ResponseEncodable`

```swift
import Vapor

struct TodosController: RouteCollection {
    func boot(routes: RoutesBuilder) throws {
        let todos = routes.grouped("todos")
        todos.get(use: index)
        todos.group(":id") { todo in 
            todo.get(use: show)
            todo.put(use: update) 
        }
    }

    func index(req: Request) async throws -> [Todo] {
        try await Todo.query(on: req.db).all()
    }

    func show(req: Request) async throws -> Todo {
        guard let todo = try await Todo.find(req.parameter.get("id"), on: req.db) else {
            throw Abort(.notFound)
        }
        return todo
    }

    func update(req: Request) async throws -> Todo {
        guard let todo = try await Todo.find(req.parameters.get("id"), on: req.db) else {
            throw Abort(.notFound)
        }
        let updatedTodo = try req.content.decode(Todo.self)
        todo.title = updatedTodo.title
        try await todo.save(on: req.db)
        return todo
    }
}
```

inside `routes.swift`
```swift
try app.register(collection: TodosController())
```