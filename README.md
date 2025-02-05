# Requests.jl

An HTTP client written in Julia. Uses `joyent/http-parser` via [HttpParser.jl](https://github.com/JuliaWeb/HttpParser.jl).

[![Build Status](https://travis-ci.org/JuliaWeb/Requests.jl.svg?branch=master)](https://travis-ci.org/JuliaWeb/Requests.jl)
[![Coverage Status](https://coveralls.io/repos/JuliaWeb/Requests.jl/badge.svg?branch=master&service=github)](https://coveralls.io/github/JuliaWeb/Requests.jl?branch=master)
[![Requests](http://pkg.julialang.org/badges/Requests_release.svg)](http://pkg.julialang.org/?pkg=Requests&ver=release)

## Quickstart

```julia
julia> Pkg.add("Requests")

julia> using Requests
```

### Make a request

```julia
get("http://httpbin.org/get")
post("http://httpbin.org/post")
put("http://httpbin.org/put")
delete("http://httpbin.org/delete")
options("http://httpbin.org/get")
```

### Add query parameters

```julia
get("http://httpbin.org/get"; query = {"title" => "page1"})
```

### Add plain text data

```julia
post("http://httpbin.org/post"; data = "Hello World")
```

### Add JSON data

```julia
post("http://httpbin.org/post"; json = {"id" => "1fc80620-7fd3-11e3-80a5"})
```

### Set headers

```julia
post("http://httpbin.org/post"; headers = {"Date" => "Tue, 15 Nov 1994 08:12:31 GMT"})
```

### Set a timeout
This will throw an error if more than 500ms goes by without receiving any
new bytes from the server.

```julia
get("http://httpbin.org/get"; timeout = .5)    # timeout = Dates.Millisecond(500) will also work
```

### File upload

The three different ways to upload a file called `test.jl` (yes this uploads the
same file three times).

```julia
    filename = "test.jl"
    post("http://httpbin.org/post"; files = [
      FileParam(readall(filename),"text/julia","file1","file1.jl"),
      FileParam(open(filename,"r"),"text/julia","file2","file2.jl",true),
      FileParam(IOBuffer(readall(filename)),"text/julia","file3","file3.jl"),
      ])
    ])
```

FileParam has the following constructors:
```julia
    immutable FileParam
        file::Union(IO,Base.File,String,Vector{Uint8})     # The file
        # The content type (default: "", which is interpreted as text/plain serverside)
        ContentType::ASCIIString
        name::ASCIIString                                  # The fieldname (in a form)
        filename::ASCIIString                              # The filename (of the actual file)
        # Whether or not to close the file when the request is done
        close::Bool
    end

    FileParam(str::Union(String,Vector{Uint8}),ContentType="",name="",filename="")
    FileParam(io::IO,ContentType="",name="",filename="",close::Bool=false)

```



### Inspect responses

```julia
type Response
    status::Int
    headers::Headers
    data::HttpData
    finished::Bool
end
```
