Dependencias:
```
$ pip install fastapi
$ pip install uvicorn["standard"]
$ npm install -g @vue/cli
$ npm install axios@0.21.1 --save
$ npm install bootstrap@4.6.0 --save
$ npm install bootstrap-vue@2.21.2 --save
```

Executar o servidor:
```
$ cd server
$ python3.11 -m venv env
$ source env/bin/activate
$ uvicorn main:app --reload
```

Executar o Vue App em um outro terminal:
```
$ cd client
$ npm install
$ npm run serve
```
