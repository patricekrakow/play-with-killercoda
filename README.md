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

## References

- <https://nodejs.org/en/download>
