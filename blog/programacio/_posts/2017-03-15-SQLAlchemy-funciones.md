---
layout: post
title: SQLAlchemy. Funciones.
date: 2017-03-15
author: Pere Vilas
tags: SQLAlchemy
---

El ORM de SQLAlchemy traduce algunas funciones comunes en muchos RDBMS. Sólo tenemos que importar *func* y tendremos accesibles las funciones descritas en el [manual](http://docs.sqlalchemy.org/en/latest/core/functions.html). También hemos importado el tipo DateTime.

Siguiendo con los ejemplos anteriores, vamos a crear el modelo **Employee** con un nuevo campo llamado hired_on. Podéis encontrar los ejemplos originales en [pythoncentral](http://pythoncentral.io/sqlalchemy-orm-examples/).

Como siempre, creamos los modelos y añadimos algunos datos. La única diferencia con las entradas anteriores es el campo *hired_on* que toma el valor *now* por defecto.

```python
# -*- coding: utf-8 -*-

from sqlalchemy import Column, String, DateTime, Integer, ForeignKey, func
from sqlalchemy.orm import relationship, backref
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()


class Department(Base):
    __tablename__ = 'department'
    id = Column(Integer, primary_key=True)
    name = Column(String)


class Employee(Base):
    __tablename__ = 'employee'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    hired_on = Column(DateTime, default=func.now())
    department_id = Column(Integer, ForeignKey('department.id'))
    department = relationship(
        Department, backref=backref('employees', uselist=True))


from sqlalchemy import create_engine
engine = create_engine('sqlite:///')

from sqlalchemy.orm import sessionmaker
session = sessionmaker()
session.configure(bind=engine)
Base.metadata.create_all(engine)

s = session()
it_department = Department(name='IT')
pepe = Employee(name='Pepe', department=it_department)
s.add(pepe)
print "Pepe antes commit: {}".format(pepe.hired_on)
s.add(it_department)
s.commit()
print "Pepe despues del commit: {}".format(pepe.hired_on)
```

> Pepe antes commit: None
Pepe despues commit: 2017-03-15 11:00:56

Ello es porque aún no hemos commitado los cambios pendientes en la sesión. En realidad *func.now()* se traduce por *now()*. Vamos a comprobarlo:

```
print func.now()
```

> now()

y por lo tanto, mientras no se postee en la DB no va ha obtener un valor.

Otras funciones útiles pueden ser: *count()*, *coalesce()*, *char_length()*, etc...

