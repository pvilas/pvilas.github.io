---           
layout: post
title: Crear un usuario S3 que accede a una sola carpeta y darle acceso con Cyberduck
---

Si usas mac y S3, seguramente te será útil dar acceso a algunos usuarios a determinadas carpetas con Cyberduck.

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

Ya tenemos acceso sólo a esta carpeta por parte del usuario.

Para accesos de usuario a carpetas determinadas, ver [este](https://aws.amazon.com/blogs/security/writing-iam-policies-grant-access-to-user-specific-folders-in-an-amazon-s3-bucket/) artículo.

