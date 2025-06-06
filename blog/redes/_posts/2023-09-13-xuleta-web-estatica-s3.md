---
layout: post
title: Xuleta crear web estàtica a S3
---

## 1. Crear el bucket

1. Crear-lo a la arrel, amb el mateix nom que volem per el website, p.e.  `hola.exemple.com`. Escollir al zona que estarà mçés propera als usuaris (veure CloudFront però).
2. Desmarcar *Block public access for this bucket*
3. Create Bucket

### Fer que el bucket sigui de tipus *Static web hosting*

Ana1r a `Properties->Static web hosting` y activar

### Canviar la bucket policy

Anar a `Permissions->Bucket policy` i posar:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::hola.exemple.org/*"
        }
    ]
}
```

## 2. Crear el registre DNS

Anar a **Route 53** i crear `hola.exemple.com`. De moment, el deixam així, posteriorment assignarem **Route trafic to** a la distribució de Cloud Front que crearem més endavant.

## 3. Crear el certificat

Anar a **Certificate manager** i demanar el certificat per a `hola.exemple.com`. Esperar uns segons, entrar a la petició i donar-li a Crear registre DNS (per validar la propietat del domini. Esperar un minut al issue. 

Refrescar pantalla de tant en tant fins que el certificat ens sigui lliurat.

## 4. Crear la distribució per apuntar a https

A **Cloud Front** creaar una distribució.

1. Escollir *Origin domain* el bucket, quan ens pregunti si volem usar el webbpoiunt, dir-li que si
2. Escollir *Redirect http to https*
3. Donar-li a CNAME i posar `hola.exemple.com`
4. Escollir el certificat
5. Guardar la distribució

Esperar un minut fins que s'activi la distribució, pujar alguna cosa al bucket i provar.

## 5. Editar el registre DNS 

ASra que ja tenim la distribució (que té un domini de tipus xyz.cloudfront.net), anar al registre de **route 53**, editar-lo i escollir **Route trafic to** a la distribució de Cloud Front creada al punt anterior.

## Extra bonus: Crear un usuari per pujar fitxers al website

Anar a *Users->Policies->Create policy* 

Escollir JSON i posar:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::*",
                "arn:aws:s3:::/*"
            ]
        }
    ]
}
```

Posar-li per nom web_file_operator o similar. Crear usuari, escollir *Attach policies directly*, escollir web_file_operator. Esperar a que es crei.

Escollir l'usuari->Security credentials->Crear access keys->Other->Create access key

**Prendre nota de l'acces key i del secret access key**.





