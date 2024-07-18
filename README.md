Execute o servidor:
```
$ cd server
$ python3 -m venv env
$ source env/bin/activate
$ uvicorn main:app --reload
```

Execute o Vue App em um outro terminal:
```
$ cd client
$ npm install
$ npm run serve
```
