# connect-role-2
It is very like connect-role module https://github.com/ForbesLindesay/connect-roles
I just change a litle code in function use3() because it clone request by req = Object.create(req); [index.js at line number 59], i think it not good
In mycode no clone code excute in function use3()

My purpose is maintain properties I assigned when I check permission

Example:
 When I check permission i had assigned
  ```javascript
  req.roleError = {Some errors to send back client}
  ```
 If I use <a href="https://github.com/ForbesLindesay/connect-roles">connect-roles</a> the property 
 ```javascript
  req.roleError 
  ```
  Will deleted when I response to client
  
  Because code in <a href="https://github.com/ForbesLindesay/connect-roles">connect-roles</a> do bellow (focus line req = Object.create(req); )
  ```javascript
  ConnectRoles.prototype.use3 = function (action, path, fn) {
  var self = this;
  if (typeof path !== 'string') throw new Error('Expected path to be of type string');
  var keys = [];
  var exp = pathToRegexp(path, keys);
  this.use2(action, function (req) {
    var pathToMatch = null;
    if(self.matchRelativePaths === true) {
      pathToMatch = req.url;
    } else {
      pathToMatch = req.app.path().replace(/\/$/, '') + req.path;
    }
    var match;
    if (match = exp.exec(pathToMatch)) {
      req = Object.create(req);
      req.params = Object.create(req.params || {});
      keys.forEach(function (key, i) {
        req.params[key.name] = match[i + 1];
      });
      return fn(req);
    }
  });
  }
  ```
  Now My code is
 ```javascript
  ConnectRoles.prototype.use3 = function (action, path, fn) {
    var self = this;
    if (typeof path !== 'string') throw new Error('Expected path to be of type string');
    var keys = [];
    var exp = pathToRegexp(path, keys);
    this.use2(action, function (req) {
        var pathToMatch = null;

        if (self.matchRelativePaths === true) {
            pathToMatch = req.url;
            var match;
            if (match = exp.exec(pathToMatch)) {
                //req = Object.create(req);
                //req.params = Object.create(req.params || {});
                keys.forEach(function (key, i) {
                    req.params[key.name] = match[i + 1];
                });
                return fn(req);
            }
        } else {
            return fn(req);
        }

    });
  }
  ```
