---
layout: post
title: SQLAlchemy. Expression Language.
date: 2017-03-15
author: Pere Vilas
tags: SQLAlchemy
---

Última entrada de esta serie y también obtenida de [pythoncentral](http://pythoncentral.io/sqlalchemy-expression-language-advanced/). Vamos a ver algunas construcciones del *Expression Language*. Se trata de constructores que emiten SQL usando una sintaxis más próxima al gestor y menos pythónica.

## Consultar todos los productos con un precio superior a los 100€

Primero construimos el select con

```
from sqlalchemy import select
ph=select([Product.id]).where(Product.price>100.00)
print ph
```

y ya podemos ejecutar la consulta

```
session.query(Product).filter(Product.id.in_(ph)).all()
```

## Carros de compra con al menos un producto de precio superior a 100

```
sc=select([ShoppingCart.id]).where(
     ShoppingCart.products.any(Product.id.in_(ph))
session.query(ShoppingCart).filter(ShoppingCart.id.in_(sc)).one()
```

## Carros sin productos cuyo precio es menor que 100

```
plh = select([Product.id]).where(Product.price < 100.00)
from sqlalchemy import not_
sc_plh= select([ShoppingCart.id]).where(
     not_(ShoppingCart.products.any(Product.id.in_(plh)))
     )
session.query(ShoppingCart).filter(ShoppingCart.id.in_(
     sc_plh)
 ).all()
```

## Carros con todos los productos de precio superior a 100

```
from sqlalchemy import and_
shopping_carts_with_all_products_higher_than_one_hundred = select([ShoppingCart.id]).where(
     and_(
         ShoppingCartProductLink.product_id.in_(product_higher_than_one_hundred),
         ShoppingCartProductLink.shopping_cart_id == ShoppingCart.id
     )
 )
session.query(ShoppingCart).filter(ShoppingCart.id.in_(
     shopping_carts_with_all_products_higher_than_one_hundred)
 ).all()

```

## Carros con importe total mayor que 200

```
from sqlalchemy import func
total_price_of_shopping_carts = select([
     ShoppingCart.id.label('shopping_cart_id'),
     func.sum(Product.price).label('product_price_sum')
 ]).where(
     and_(
         ShoppingCartProductLink.product_id == Product.id,
         ShoppingCartProductLink.shopping_cart_id == ShoppingCart.id,
     )
).group_by(ShoppingCart.id)
session = DBSession()
session.query(total_price_of_shopping_carts).all()
```

> [(1, 450.0), (2, 30.0)]

```
session.query(ShoppingCart).filter(
     ShoppingCart.id == total_price_of_shopping_carts.c.shopping_cart_id,
     total_price_of_shopping_carts.c.product_price_sum > 200.00
 ).all()
```

> [( :John:[( :u'CPU':300.0 ), ( :u'Motherboard':150.0 )] )]

```
session.query(ShoppingCart).filter(
     ShoppingCart.id == total_price_of_shopping_carts.c.shopping_cart_id,
     total_price_of_shopping_carts.c.product_price_sum < 200.00
 ).all()
```

> [( :John:[( :u'Coffee Machine':30.0 )] )]
