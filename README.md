<p align="center">
  <img src="https://taky.s3.amazonaws.com/71pixzi8dsgq.svg" width="75">
</p>

`link-shortener` is a quick api url shortener. it will pass/merge query strings and has basic reports as well.

---

### create link
```
http://localhost:10001/links?method=post&url=http%3A%2F%2Fnationalsolardepartment.com%2Fveteran.html%3Flp%3Dveteran%26note%3DXinyi_CV_Imgtest_Vet_6_TT_2%26sxid%3Da8ngjp37jy5x
```

```js
{
    "ok": true,
    "response": {
        "__v": 0,
        "_id": "21akbzbfcpwi",
        "ctime": 1490134019,
        "mtime": 1490134019,
        "url": "http://nationalsolardepartment.com/veteran.html?lp=veteran&note=Xinyi_CV_Imgtest_Vet_6_TT_2&sxid=a8ngjp37jy5x",
        "active": true
    },
    "_meta": {
        "path": "/links",
        "body": {
            "url": "http://nationalsolardepartment.com/veteran.html?lp=veteran&note=Xinyi_CV_Imgtest_Vet_6_TT_2&sxid=a8ngjp37jy5x",
            "pretty": "1"
        },
        "query": {},
        "filter": {},
        "elapsed": 4
    }
}
```

### public redirect
```
http://localhost:10001/~21akbzbfcpwi
```

### public stats link
```
http://localhost:10001/+21akbzbfcpwi?days=15
```

```js
{
    "ok": true,
    "response": {
        "03/07/2017": {},
        "03/08/2017": {},
        "03/09/2017": {},
        "03/10/2017": {},
        "03/11/2017": {},
        "03/12/2017": {},
        "03/13/2017": {},
        "03/14/2017": {},
        "03/15/2017": {},
        "03/16/2017": {},
        "03/17/2017": {},
        "03/18/2017": {},
        "03/19/2017": {},
        "03/20/2017": {},
        "03/21/2017": {
            "impression": 1
        }
    },
    "_meta": {
        "path": "/-51akcbr9jaei",
        "body": {},
        "query": {},
        "filter": {},
        "elapsed": 5
    }
}
```

### permissions
rest routes are public (`/links`, `/events`), protect those routes
if you don't want people deleting your links

---

## based on `mkay`

<p align="center">
  <img src="https://taky.s3.amazonaws.com/11nckfunnxkm.svg" width="245">
</p>

`mkay` is a framework that that allows you to develop complex backends quickly
by centralizing the bulk of your work inside of models and libraries.

---

# glancing
- iced/express/mongoose/winston
- mongodb/redis/memcached
- cluster support
- generate everything, produce complex apis rapidly
  - chmod +x bin generators for all significant application files (models,
    crons, routes) in respective folders
- output middleware (`res.respond()`)
  - json/pretty (`?format=json&pretty=1`)
  - jsonp
  - xml
- crud/rest doesn't need to be generated, it's just exposed via model export
  - method override allows for easy testing using the browser (`?method=post`)
  - coffeescript json query filters and sort selectors
  - lean queries and field selection
  - pagination (`?limit=&page=`)
- flexible
  - add custom routes
  - append auto-exposed model crud routes, make cache layers over mongo,
    whatever
  - configure which models/methods/statics you want exposed and which path
    prefixes you want to use
- automatic recognize existence of `./static` and exposes it using
  `express-static`
- automatic recognize existence of `./views/*.hbs` and configures `res.render`
  with `swag.js` and other custom helper methods (`./lib/hbs_helpers`)
- cookie-sessions baked, togglable with config option

# quick start

```bash
git clone https://github.com/tosadvisor/mkay
cd ./mkay
sudo npm i -g iced-coffee-script
npm i
iced app.iced
```

<img src="https://taky.s3.amazonaws.com/81hnpkj7468m.png" width="2046">

### generate a model

```bash
cd ./models
./_create --name friends > friends.iced
```

now you have a rest api and don't have to write routes that wrap the model
methods, the methods can just be exposed directly.

- crud is automatically exposed if `model.AUTO_EXPOSE` is exported
- expose instance methods using `model.AUTO_EXPOSE.methods=[]`
- expose static methods using `model.AUTO_EXPOSE.statics=[]`
- select the path by setting `model.AUTO_EXPOSE.route`, otherwise it will use
  the filename

### create a new "friend"

`http://localhost:10001/friends?method=post&name=John`

- list `/friends`
- view `/friends/:_id`
- edit `/friends/:_id/?method=post&name=James`
- delete `/friends/:_id/?method=delete`

### create/expose a model instance method

add a method to `FriendsSchema`- auto-exposed methods must always
take an object as the first parameter and a callback as the second

```coffeescript
FriendsSchema.methods.change_name = ((opt={},cb) ->
  if !opt.name then return cb new Error "`opt.name` required"
  @name = opt.name
  @save cb
)
```

now add the function name to the `model.AUTO_EXPOSE.methods` array

```coffeescript
model.AUTO_EXPOSE = {
  route: '/friends'
  methods: [
    'change_name'
  ]
  statics: []
}
```

now run it through the browser, it converts `req.query` into `opt`, runs the
method and returns the result to the browser

`http://localhost:10001/friends/:_id/change_name?method=post&name=Jose`

---

# global ns pollutants
- `db` mongojs instance
- `db.<Model>` (mongoose models loaded into `db`)
- `redis` ioredis instance
- `memcached` node-memcached instance
- `conf` configuration object
- `eve` eventemitter2 instance
- `log` winston instance
- `ll` alias for console.log
- `lp` alias for console.log pretty print (`JSON.stringify obj, null, 2`)

# auto-loaded
- models located in `./models`
- routes located in `./routes` are loaded using the base file name minus the
  extension as the path prefix
- files in `./cron` are automatically `required`, a bare-bones generator is
  provided
- if `./views` exists `.hbs` files are renderable via `res.render()`
- if `./static` exists it is automatically served using express' static
  middleware

# chmod +x generators
- `./crons/_create`
- `./models/_create`
- `./routes/_create`

---

### License: MIT

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is furnished
to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

