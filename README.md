# rotki.com

## Build Setup

```bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn dev

# build for production and launch server
$ yarn build
$ yarn start

# generate static project
$ yarn generate
```

## Run

Make sure the environment file exists

```bash
$ touch .env
```

And input the RECAPTCHA public key there.

```
RECAPTCHA_SITE_KEY=XXXX
```

if you are running behing an https proxy make sure to also add
```
BASE_URL=https://localhost
NODE_TLS_REJECT_UNAUTHORIZED=0
```

Run with

```bash
$ yarn dev
```


## Lint

To fix any lint errors you have to run

```bash
yarn lint:js --fix
```
