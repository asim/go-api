# httpResponse

httpResponse is a object field in a message.It can straightly operate  http reponse writer.

## Ussage
Assume you've made a `user.proto`.
If you want to straightly operate the response writer, add `Wrap`,`Pair`,`HttpResponse` below, and insert the httpResponse as an object field in the `Response`:

```proto
syntax = "proto3";

service User {
    rpc GetToken(Request) returns (Response) {}
}

message Request {
    string username = 1;
    string password = 2;
}

message Response {
    HttpResponse httpResponse = 1;
    string token = 2;
    string exp = 3;
}

message HttpResponse {
        int32 statusCode = 1;
        map<string, Pair> header = 2;
        string body = 3;
}
message Pair {
    string key = 1;
    repeated string values = 2;
}

message Wrap {
    HttpResponse httpResponse = 1;
}

```
cd where the user.proto is  and
generate its service by

`protoc --proto_path=. --micro_out=. --go_out=. user.proto`

you can operate your own writer's status code and body:
```go
func (u *User) GetToken(c context.Context, req *user.Request, rsp *user.Response) error {
    rsp.Exp = time.Now().Add(24 * time.Hour).Local().String()
    rsp.Token = "Bearer ea1jf2ad253d1da8a7d0f9g98ad9z9fas09dkv5.24csad.142.sc"
    rsp.HttpResponse = &user.HttpResponse{}
    rsp.HttpResponse.StatusCode=400
    rsp.HttpResponse.Body = "hello"
    return nil
}
```
