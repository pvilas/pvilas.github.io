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
make
```

Ara que ja tenim el mòdul (.so), el copiam a /usr/local/etc

```cp build/redisearch.so /usr/local/etc```

Afeixim mòdul a conf perque el carregui automàticament

```echo "loadmodule /usr/local/etc/redisearch.so" >> /usr/local/etc/redis.conf```

Arrancar Redis

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

La [pàgina principal](https://oss.redislabs.com/redisearch/) del mòdul i el [getting started](https://github.com/RediSearch/redisearch-getting-started).

#### Insertar dades

RediSearch treballa només sobre taules hash, així que usam aquests, p.e.

```
> HSET movie:11002 title "Star Wars: Episode V - The Empire Strikes Back" plot "After the Rebels are brutally overpowered by the Empire on the ice planet Hoth, Luke Skywalker begins Jedi training with Yoda, while his friends are pursued by Darth Vader and a bounty hunter named Boba Fett all over the galaxy." release_year 1980 genre "Action" rating 8.7 votes 1127635 imdb_id tt0080684
```

Comprovam

```
> HMGET movie:11003 title rating

1) "Star Wars: Episode V - The Empire Strikes Back"
2) "8.7"
```

El problema ara és recuperar tots els movies que s'estrenaren el 1980: necessitam un índex invers que els relacioni 

| Index entry | Value (list) |
| idx:movie:release_year/ 1980 | movie:001, movie:034, ... |

#### Creació de l'índex

```
ft.create idx:movie on hash prefix 1 "movie:" schema title text sortable plot text sortable release_year numeric sortable genre tag sortable
```

Obtenim informació amb

```
> ft.info idx:movie
```

Llistar els índexs

```
> ft._list
```
