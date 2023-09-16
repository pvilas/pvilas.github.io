---
layout: post
title: Xuleta crear web estàtica a S3
---

## Crear el bucket

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

## Crear el registre DNS

Anar a **Route 53** i crear `hola.exemple.com`

## Crear el certificat

Anar a **Certificate manager** i demanar el certificat per a `hola.exemple.com`. Esperar uns segons, entrar a la petició
i donar-li a Crear registre DNS (per validar la propietat del domini. Esperar un minut al issue. 

Refrescar pantalla de tant en tant fins que el certificat ens sigui lliurat.

## Crear la distribució per apuntar a https

A **Cloud Front** creaar una distribució.

1. Escollir *Origin domain* el bucket, quan ens pregunti si volem usar el webbpoiunt, dir-li que si
2. Escollir *Redirect http to https*
3. Donar-li a CNAME i posar `hola.exemple.com`
4. Escollir el certificat
5. Guardar la distribució

Esperar un minut fins que s'activi la distribució, pujar alguna cosa al bucket i provar.
