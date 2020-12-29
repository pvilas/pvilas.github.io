---           
layout: post
title: Instalar Redis a OSX
tags: redis
date: 2020-12-29
author: Pere Vilas
---

## Instal.lació

```brew install redis```

### Compilar Redisearch 

```brew install cmake
git clone --recursive https://github.com/RediSearch/RediSearch.git 
cd RediSearch
make```

Ara que ja tenim el mòdul (.so), el copiam a /usr/local/etc

```cp build/redisearch.so /usr/local/etc```

Afeixim mòdul a conf perque el carregui automàticament

```echo "loadmodule /usr/local/etc/redisearch.so" >> /usr/local/etc/redis.conf```

Arracar Redis

```brew services start redis```

Comprovam si s'ha carregat es mòdul *search*

```redis-cli module list```

Els executables estan a

```
/usr/local/bin/redis-cli
/usr/local/bin/redis-server
```

Si no volem arrancar com a servei, feim `redis-server`

### Tutorial de RediSearch

El [getting started](https://github.com/RediSearch/redisearch-getting-started).


