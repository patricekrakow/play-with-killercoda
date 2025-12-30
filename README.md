# Let's Play with Killercoda

I am *BIG* fan of [Killercoda](https://killercoda.com/) and especially their [Ubuntu Playground](https://killercoda.com/playgrounds/scenario/ubuntu) that I am using of all sorts of exploration.

There is only one ***ANNOYING*** anti-feature being that you can only access HTTP services running on **all** interfaces (like `0.0.0.0`), which means that when you want to test an HTTP service running just on `localhost`, it does not work :-( This is very frustrating... until I find a workaround that I would like to share here ;-)

Let's first show the issue using the *classic* HTTP server you can get from a few lines of JavaScript using [Node.js](https://nodejs.org/en).

```javascript
// server.mjs
import { createServer } from 'node:http';

const server = createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World!\n');
});

// starts a simple http server locally on port 3000
server.listen(3000, 'localhost', () => {
  console.log('Listening on localhost:3000');
});

// run with `node server.mjs`
```

We first need to [install Node.js](https://nodejs.org/en/download) on our [Ubuntu Playground](https://killercoda.com/playgrounds/scenario/ubuntu) using the following commands:

```text
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

```text
\. "$HOME/.nvm/nvm.sh"
```

```text
nvm install 24
```

```text
node --version # Should print "v24.12.0".
```

```text
npm -v # Should print "11.6.2".
```

Then, we can create a file `server.mjs` and copy-paste the content above:

```text
vi server.mjs
```

And, finally, we can run this simple HTTP server that runs on `localhost` only:

```text
node server.mjs
```

We can verify that it runs correctly by using `curl` in another terminal:

```text
curl localhost:3000
```

which should give the following response:

```text
Hello World!
```

**But**, if you click on the little _hamburger_ icon in the top-right corner, select "Traffic /Ports", type `3000` in the "Custom Ports" edit box and click on the "Access" button, you get an annoying "502 Bad Gateway" response, according to the documentation which says: _"The services need to run on all interfaces (like 0.0.0.0) and not just localhost"_.

If you are the owner of the source code, like in this situation, the fix is easy, you just follow the documenation and change `localhost` to `0.0.0.0` and it will work. Have a try:

```javascript
// server.mjs
import { createServer } from 'node:http';

const server = createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World!\n');
});

// starts a simple http server locally on port 3000
server.listen(3000, '0.0.0.0', () => {
  console.log('Listening on 0.0.0.0:3000');
});

// run with `node server.mjs`
```

Let's now revert the situation to `localhost` and see how we can workaround the limitation in another way :-)

> A realistic example would be if you want to test the [Develop with containers](https://docs.docker.com/get-started/introduction/develop-with-containers/) hands-on guide from [Docker](https://docs.docker.com/). That's actually why I search for a workaround ;-)

The trick is to use a NGINX instance with the following reverse proxy configuration:

```nginx
events {
  worker_connections 768;
}

http {
  server {
    listen 0.0.0.0:8080;

    location / {
      proxy_pass http://localhost:3000/;
    }

  }
}
```

You will then have an HTTP service running on `0.0.0.0` (port `8080`, or the one you like) and the only thing it will do is to transfer the requests to `localhost` (port `3000`).

Let's first install NGINX:

```text
sudo apt update
```

```text
sudo apt install -y nginx
```

```text
systemctl status nginx
```

You can test the installation by clicking on the little _hamburger_ icon in the top-right corner, select "Traffic /Ports" and click on `80` in the "Common Ports" section.

Let's backup the origniam `nginx.conf` file:

```text
sudo mv /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```

and create our new `nginx.conf` file:

```text
sudo vi /etc/nginx/nginx.conf
```

to copy-paste the content above.

Then, we still need to update NGINX with this new configuration:

```text
sudo nginx -s reload
```

You can test this configuration by clicking on the little _hamburger_ icon in the top-right corner, select "Traffic /Ports" and click on `8080` in the "Common Ports" section. You should now see the response coming from the HTTP service running just on `localhost`. **Yes!**

## References

- <https://nodejs.org/en/download>
