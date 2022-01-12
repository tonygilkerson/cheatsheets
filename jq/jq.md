# JQ

* [a cheetsheet gist](https://gist.github.com/olih/f7437fb6962fb3ee9fe95bda8d2c8fa4)

## User Data Examples

In this section I will use the user data from jsonplaceholder as input for the following examples; [users](https://jsonplaceholder.typicode.com/users) and [todos](https://jsonplaceholder.typicode.com/todos).

The data is stored here in the [user.json](user.json) and [todos.json](todos.json) files. Below is a sample of the data:

User sample

```json
[
  {
    "id": 1,
    "name": "Leanne Graham",
    "username": "Bret",
    "email": "Sincere@april.biz",
    "address": {
      "street": "Kulas Light",
      "suite": "Apt. 556",
      "city": "Gwenborough",
      "zipcode": "92998-3874",
      "geo": {
        "lat": "-37.3159",
        "lng": "81.1496"
      }
    },
    "phone": "1-770-736-8031 x56442",
    "website": "hildegard.org",
    "company": {
      "name": "Romaguera-Crona",
      "catchPhrase": "Multi-layered client-server neural-net",
      "bs": "harness real-time e-markets"
    }
  }
]
```

Todo example

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": false
  }
]
```

Here are a few basic examples to get started.

```sh
# cat the file as json formated
jq '.' user.json

# list user names
jq '.[].username' user.json

# list user names without quotes
jq -r '.[].username' user.json

# output each element of the array 
# note the output is not valid json, you end up with someting like
#  {doc 1}{doc 2}{doc 3}...
#  Not in an array and no comma between the docs
jq '.[]'  user.json > user.txt
cat user.txt

# To slurp up the docs into an array 
# With the following you will end up with
# [{doc 1}{doc 2}{doc 3}...]
jq -s '.' user.txt

# concat fields
jq -r '.[] | .username + .email + .company.name'  user.json

# but that is ugly lets add | as a delimiter 
jq -r '.[] | .username + "|" + .email + "|" + .company.name'  user.json

# now we can pipe the output to awk to create a report
jq -r '.[] | .username + "|" + .email + "|" + .company.name'  user.json | awk -F "|" \
 '
  BEGIN{
    format = "%-25s %-25s %s\n";
    printf format, "User","Company","Email"
  }
  {
    printf format, $1,$3,$2
  }
'
```

```sh
# This will combine the data from both file like this
# [user docs],[todo docs]
# such that 
#  .[0] refers to users
#  .[1] refers to todos
# 
jq -s '.' user.json todos.json

# This will combine the arrays into one
# [ {user1}, {user2}, {user3}..., {todo1}, {todo2},{todo3}...]
jq -s '.[0] + .[1]' user.json todos.json

# But that is not what we want, 
# we want to "join" the user doc with the corresponding todo docs

```

 ## WORK IN PROGRESS