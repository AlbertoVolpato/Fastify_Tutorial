# *Fastify*
Fastify è un Framework web veloce e con sovraccarico ridotto, per Node.js
#
## Caratteristiche principali: 
Altamente performante: per quanto ne sappiamo, Fastify è uno dei framework web più veloci in città, a seconda della complessità del codice possiamo servire fino a 30mila richieste al secondo.

Estensibile: Fastify è completamente estensibile tramite i suoi hook, plugin e decoratori.

Basato su schema: anche se non è obbligatorio, consigliamo di utilizzare JSON Schema per convalidare i tuoi percorsi e serializzare i tuoi output, internamente Fastify compila lo schema in una funzione altamente performante.

Logging: i log sono estremamente importanti ma costosi; abbiamo scelto il miglior logger per eliminare quasi questo costo, Pino !
Developer friendly: il framework è costruito per essere molto espressivo e per aiutare gli sviluppatori nel loro uso quotidiano, senza sacrificare prestazioni e sicurezza.

Pronto per TypeScript: lavoriamo sodo per mantenere un file di dichiarazione del tipo TypeScript in modo da poter supportare la crescente comunità di TypeScript.

# 
## quindi perchè usarlo? 

## in poche parole è molto più veloce di express e ha una serie di estensioni che lo rendono meno grezzo di altri framework 
#
## come iniziare:
per prima cosa abbiamo ovviamente bisogno di node.js installato,
successivamente io consiglio di generare il progetto attraverso la CLI poichè porta con se una serie di benefici che successivamente andrò ad elencare, comunque procedere con il comando: 

```
 $ npm install --global fastify-cli
```

e successivamente generare il progetto attraverso 

```
 $ fastify generate "Nome del Progetto"
```

una volta fatto ciò all'interno della cartella appena generata troveremo una serie di file e cartelle generate, per prima cosa dobbiamo installare le dipendeze npm perciò apriamo il terminale all'interno della cartella del nostro progetto e facciamo un bel:
```
 $ npm install
```

fatto ciò il server di base è gia runnabile attraverso:
```
 $ npm start 
```

la prima cosa che raccomando di fare prima di iniziare a scrivere codice è quella di entrare nel package.json e nella riga n 11
troveremo il seguente comando     
```
"start": "fastify start -l info app.js",
```

da qui possiamo compiere una serie di operazioni (che usando express dovremmo compiere scrivendo codice) necessarie per cambiare il numero di porta del server o per porre l'indirizzo ip a 0.0.0.0 operazione necessaria pochè in caso contrario il server non sarà raggiungibile nella rete globale 
per farlo procediamo a sostituirlo con il seguente comando: 
```
"start": "fastify start -l info app.js -p <"numero porta da utilizzare (3000 di default)"> -a 0.0.0.0",
```

fatto ciò siamo in grado di proseguire scrivendo il codice, precedentemente tuttavia volevo sottolineare una serie di peculiarità della cli fastify, possiamo notare tre cartelle ovvero plugins routes e test, personalmente il utilizzo solamente la seconda citata, tuttavia è necessario averle tutte e tre in caso contrario ci verrà restituito un errore in fare si start, in ongi caso all'intenro delle cartelle precedentemente citate sono presenti dei readme che illustrano l'utilità di queste ultime, e una sottocartella example, essa è posta unicamente come esempio, perciò si può tranquillamente procedere a cancellarla se lo si desidera, mentra i file all'interno delle cartelle sono posti per creare l'esempio iniziale del server, anchessi sono inutili.

# INIZIO PROGRAMMAZIONE 

se avete seguito tutti i passi precedenti vi ritroverete un file app.js e al suo interno dovrebbe apparire cosi: 
```

'use strict'

const path = require('path')
const AutoLoad = require('@fastify/autoload')

// Pass --options via CLI arguments in command to enable these options.
module.exports.options = {}

module.exports = async function (fastify, opts) {
  // Place here your custom code!

  // Do not touch the following lines

  // This loads all plugins defined in plugins
  // those should be support plugins that are reused
  // through your application
  fastify.register(AutoLoad, {
    dir: path.join(__dirname, 'plugins'),
    options: Object.assign({}, opts)
  })

  // This loads all plugins defined in routes
  // define your routes in one of these
  fastify.register(AutoLoad, {
    dir: path.join(__dirname, 'routes'),
    options: Object.assign({}, opts)
  })
}
```
come ci suggerisce il team Fastify attravero i commenti inseriti, non dobbiamo assolutamente toccare il codice già fatto poichè implementa già fastify autoload che carica automaticamente plugin e routes,

consiglio di porre eventuali estensioni installate al di sotto del commento:  // Place here your custom code!

le estensioni si installano attraverso npm

tra le estensioni è praticamente obbligatorio installare il CORS , e il provider necessario per il database, qui sotto inserisco alcuni esempi per i principali database: 

```
MONGODB:
Estensione: npm i @fastify/mongodb

Codice per la connessione da inserire in app.js : 

fastify.register(require('@fastify/mongodb'), {
  // force to close the mongodb connection when app stopped
  // the default value is false
  forceClose: true,
  
  url: 'mongodb://mongo/mydb'
})
```
```
POSTGRES: 
Estensione: npm i pg @fastify/postgres

Codice per la connessione da inserire in app.js : 

fastify.register(require('@fastify/postgres'), {
  connectionString: 'postgres://postgres@localhost/postgres'
})
```

per installare il cors: 
```
Estensione: npm i @fastify/cors

Codice: 

import cors from '@fastify/cors'

await fastify.register(cors, { 
  // put your options here
})

```
NOTA BENE! LE ESTENSIONI SONO ALL'INTERNO DI UN MODULE EXPORT PERCIO' NON VANNO IMPORTATE UNA SECONDA VOLTA SUI FILE CHE ANDREMO A CREARE MA SARANNO LEGGIBILI GLOBALMENTE ALL'INTERNO DELLA CARTELLA DEL PROGETTO


Una volta fatto ciò il server si connetterà ai nostri database, procediamo dunque a eseguire le prime query; 

aprimao la cartella routes e al suo interno creiamo un file .js, ipoteticamente post.js e copiamo al suo intenrno il seguente codice: 
```
'use strict'

module.exports = async function (fastify, opts) {
  
}
```
NOTA BENE, TUTTO IL CODICE CHE ANDRAI A SCRIVERE LO DOVRAI SCRIVERE ALL'INTENRO DEL module.exports CON LA SOLA ECCEZIONE DELLE LIBRERIE COME:
```
 const multer = require('fastify-multer') // or import multer from 'fastify-multer'
```

LE QUALI ANDRANNO AL DI FUORI DI ESSO

procediamo quindi ad eseguire le richieste CRUD.

baserò l'intero tutorial per postgres sql ma sul sito di fastify o sulla loro repo GitHub sono presenti le istuzioni per qualunque database


Qui sotto sono presenti tutte le oparazioni (get post put delete ecc.)

```
GET CON UN ID 
fastify.get('/username/:id', async (req, reply) => {
        const client = await fastify.pg.connect()
        const id = req.params.id;
        try {
            const { rows } = await client.query(
                'SELECT * FROM users WHERE username = $1', [id]
            )
            return rows
        } finally {
            client.release()
        }
    })
```

```
GET GENERICO 
fastify.get('/user', async (req, reply) => {
        const client = await fastify.pg.connect()
        try {
            const { rows } = await client.query(
                'SELECT * FROM users',
            )
            return rows
        } finally {
            client.release()
        }
    })
```
```
POST
fastify.post('/post-like', async (req, reply) => {
        const client = await fastify.pg.connect()
        try {
            const { user_id, post_id } = req.body;
            const { rows } = await client.query(
                'INSERT INTO post_likes (user_id,post_id) VALUES ($1,$2) RETURNING id',
                [user_id, post_id]
            )
            return rows
        } finally {
            client.release()
        }
    })
```
```
PUT 
fastify.put('/user/:id', async (req, reply) => {
        const client = await fastify.pg.connect()
        try {
            const { username, email, password } = req.body;
            const id = req.params.id;
            const { rows } = await client.query(
                'UPDATE users SET username = $1, email = $2, password = $3 WHERE user_id = $4  RETURNING user_id',
                [username, email, password, id]
            )
            return rows
        } finally {
            client.release()
        }
    })
```

```
DELETE 
fastify.delete('/user/:id', async (req, reply) => {
        const client = await fastify.pg.connect()
        try {
            const id = req.params.id;
            const { rows } = await client.query(
                'DELETE FROM users WHERE user_id = $1 RETURNING user_id', [id]
            )
            return rows
        } finally {
            client.release()
        }
    })
```
CON QUESTO SARAI IN GRADO DI SVILUPPARE UN SERVER REST COMPLETO 


PER L'UPLOAD DEI FILE UTILIZZIAMO fastify-multer 

per installarlo 
```
$ npm i fastify-multer
```

SCRIVEREMO POI: 
```
const multer = require('fastify-multer') // or import multer from 'fastify-multer'

module.exports = async function (fastify, opts) {
    fastify.register(multer.contentParser)
}
```

SARA' POI NECESSARIO CREARE UNO STORAGE OVVERO UNO STAMPO PER APPROVARE I FILE E CONSERVARLI IN UNA DIRECTORY:
```
const multer = require('fastify-multer') // or import multer from 'fastify-multer'

module.exports = async function (fastify, opts) {
    fastify.register(multer.contentParser)

    var storage = multer.diskStorage({ //multers disk storage settings
        destination: function (req, file, cb) {
            cb(null, 'uploads/picture')
        },
        filename: function (req, file, cb) {
            var id = crypto.randomUUID()
            cb(null, id + '.' + file.originalname.split('.')[file.originalname.split('.').length - 1])
        },
        onFileUploadStart: function (file) {
            console.log("Inside uploads");
            if (file.mimetype == 'image/jpg' || file.mimetype == 'image/jpeg' || file.mimetype == 'image/png') {
                return true;
            }
            else {
                return false;
            }
        }
    });
}
```
IN DESTINATION LA STRINGA CONTENTENTE ('upload/picture') INDICA LA DESTINAZIONE DEL FILE, IN QUESTO CASO ALL'INTERNO DELLA CARTELLA PICTURE a SUA VOLTA CONTENUTA NELLA CARTELLA UPLOAD (intenra alla cartella del server)

IN QUESTO CASO IL NOME DELL'IMMAGINE VIENE CRYPTATO ATTRAVERSO LA LIBRERIA : crypto

se pertanto il tuo scopo è quello di conservare l'immagine in modo differente basterà sostituire il " var id  " con un file.originalname per salvare il file tale quale a quello inviato

INFINE AI PIEDI DI QUESTO CODICE E'NECESSARIO INSERIRE IL SEGUENTE PEZZO DDI CODICE:


```
 var upload = multer({ //multer settings
        storage: storage
    }).single('picture');
```
qui all'intenro possiamo modificare l'ultima parte in cui specifichiamo .single('picture') specificando quindi che il file andrà inviato tramite 'picture'

utilizzando postman quindi:

![](Immagine%202023-01-06%20223149.png)

inoltre il .single specifica che il server accetta una sola immagine per richiesta, è possibile accettare più di un file attraverso: 
```
.array('pictures', <number_of_pictures>)

```
