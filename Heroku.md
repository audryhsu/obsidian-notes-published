---
creation date: 2022-01-31 12:40
aliases: 
tags: 🖥️
---

# [Heroku](Heroku.md)
---
Heroku is a web-based service that lets people deploy applications without the stress, hassle, and expense of setting up a private Web server.

How to use from the command line:
1. `$ heroku login` - opens in the browser to log in to Heroku account and authorizes heroku CLI
2. `$ heroku create` returns the url for your app and the url of the heroku remote git repo

```bash
`$ heroku create
Creating app... done, ⬢ secure-spits-79630
https://general-nuisance-79630.herokuapp.com/ | https://git.heroku.com/general-nuisance-79630.git`
```

3. Create a development config file for development specific settings
-   The value of the `secret` property in the session object
-   The location of the database (`localhost`, in this case)
-   The name of the database (`todo-lists`, in this case)
-   The host and port that we want to listen to for connections

Create a `.env` file to store the above information. Make sure to `.gitignore` `.env` files since its specific to development
```bash
# .env file

SECRET="this is not very secure"
DATABASE_URL="postgresql://localhost/todo-lists"
HOST="localhost"
PORT="3000"

```

Download the `dotenv` module to manage multiple configurations  (typical to have testing, development, and product). 

4. Create production config
Use `heroku config:set` command to tell Heroku what config values to use
- Heroku will set the port and database url, so we don't need to provide those
- pass in name of app with `-a` flag
```bash
# configure production settings

ahsu@ahsu:~$ heroku config:set SECRET="Dizis m0r3 Secur3.than-b4!" -a nameless-headland-26422
Setting SECRET and restarting ⬢ nameless-headland-26422... done, v3
SECRET: Dizis m0r3 Secur3.than-b4!
ahsu@ahsu:~$ heroku config:set HOST="0.0.0.0" -a nameless-headland-26422
Setting HOST and restarting ⬢ nameless-headland-26422... done, v4
HOST: 0.0.0.0
```

See [Todo App Database Walkthrough](./Todo%20App%20Database%20Walkthrough.md) for deployment specific instructions.

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Deploying nodejs](https://devcenter.heroku.com/articles/deploying-nodejs)

Related: 