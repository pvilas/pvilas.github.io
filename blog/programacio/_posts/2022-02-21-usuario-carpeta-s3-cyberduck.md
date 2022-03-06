---           
layout: post
title: Crear un usuario S3 que accede a una sola carpeta y darle acceso con Cyberduck
---

Si usas mac y S3, seguramente te será útil dar acceso a algunos usuarios a determinadas carpetas con Cyberduck.

## Acceso a un bucket

Primero creamos la "policy" para el usuario desde IAM.

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
                "arn:aws:s3:::MI_BUCKET",
                "arn:aws:s3:::MI_BUCKET/*"
            ]
        }
    ]
}
```

Después, creamos el usuario y le asignamos la policy anteriormente creada. Tenemos que crearle un acceso programático,apuntamos Access Key y Secret Access Key.

En Cyberduck, creamos una nueva conexión de tipo S3 y en el nombre del servidor ponemos 

```
MY_BUCKET.s3.amazonaws.com
```

Ya tenemos acceso sólo a este bucket por parte del usuario.

## Acceso sólo a carpetas determinadas dentro de un bucket

Para accesos de usuario a carpetas determinadas, ver [este](https://aws.amazon.com/blogs/security/writing-iam-policies-grant-access-to-user-specific-folders-in-an-amazon-s3-bucket/) artículo.

Tengamos en cuenta que S3 no usa una estructura de directorio propiamente dicha sino que esta es plana. Restringir el acceso a determinadas "carpetas" se hace a través de "condiciones" en el nombre del recurso al que queremos acceder.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowUserToSeeBucketListInTheConsole",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListAllMyBuckets"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::*"
            ]
        },
        {
            "Sid": "AllowRootAndHomeListingOfCompanyBucket",
            "Action": [
                "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::MY_BUCKET"
            ],
            "Condition": {
                "StringEquals": {
                    "s3:prefix": [
                        "",
                        "MY_DIR/"
                    ],
                    "s3:delimiter": [
                        "/"
                    ]
                }
            }
        },
        {
            "Sid": "AllowListingOfUserFolder",
            "Action": [
                "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::MY_BUCKET"
            ],
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "MY_DIR/*"
                    ]
                }
            }
        },
        {
            "Sid": "AllowAllS3ActionsInUserFolder",
            "Effect": "Allow",
            "Action": [
                "s3:*"
            ],
            "Resource": [
                "arn:aws:s3:::MY_BUCKET/MY_DIR/*"
            ]
        }
    ]
}

```
