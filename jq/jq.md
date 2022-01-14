# JQ

## Resources

* [jqplay.org](https://jqplay.org/)
* a cheetsheet gist - [here](https://gist.github.com/olih/f7437fb6962fb3ee9fe95bda8d2c8fa4)
* jq lession on programminghistorian.org - [here](https://programminghistorian.org/en/lessons/json-and-jq)


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

A todo example

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

To follow along clone this project and `cd jq` run the examples below to see the output. 

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

Now lets see if we can join the two files.  We want to match up each doc on a common field and combine the doc. The user has `id` and the todos have `userId`. Lets give it a go. 

```sh
# First we need the user doc to have a userId to match todo
# the result looks like:
jq '.[] | { userId: .id }' user.json 

# Now lets add the other user fields
# Whit this we have our originial user doc but with userId included
jq '.[] | { userId: .id } + .' user.json 

# Make into an array and save to new file
jq '[.[] | { userId: .id } + .]' user.json > user-modified.json
# or lets assume we only want userId and email
jq '[.[] | { userId: .id, email: .email }]' user.json > user-modified.json


# This will combine the arrays into one
# [ {user1}, {user2}, {user3}, {todo1}, {todo2}, {todo3} ]
jq -s '.[0] + .[1]' user-modified.json todos.json 

# Group by userId
# [ {user1, todo:[{},{},{}]}, {user2, todo:[{},{},{}]}... ]
jq -s '[.[0] + .[1] | group_by(.userId)[] | .[0].userId as $u | {userId: $u, todos: .[1:]}]' user-modified.json todos.json 
```

