---
layout: post
title: Modelos auditados en SQLAlchemy
author: Pere Vilas
tags: SQLAlchemy
---

Podemos fácilmente registrar quién y cuando operó sobre un modelo con la siguiente estrategia. Por supuesto podra extenderse para crear un log de cambios, aunque en este caso deberamos usar la versión after_insert, after_update y after_delete.

```python
# -*- coding: utf-8 -*-

from sqlalchemy import Column, String, Integer, ForeignKey, DateTime, func
from sqlalchemy.orm import relationship, backref
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm.interfaces import MapperExtension
import datetime

# from flask_security import current_user

Base = declarative_base()


class BaseExtension(MapperExtension):
    """Base entension class for all entity """

    def before_insert(self, mapper, connection, instance):
        """ set the created_at  """
        instance.created_at = func.now()
        # instance.created_by_user = current_user

    def before_update(self, mapper, connection, instance):
        """ set the updated_at  """
        instance.updated_at = func.now()
        # instance.updated_by_user = current_user


class AuditedModel(object):
    __mapper_args__ = {'extension': BaseExtension()}

    created_by_user = Column(String)
    created_at = Column(DateTime)
    updated_by_user = Column(String)
    updated_at = Column(DateTime)


class Department(Base, AuditedModel):
    __tablename__ = 'department'
    id = Column(Integer, primary_key=True)
    name = Column(String)


class Employee(Base, AuditedModel):
    __tablename__ = 'employee'
    id = Column(Integer, primary_key=True)
    name = Column(String)
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
s.add(Employee(name='Pepe', department=it_department))
s.add(Employee(name='Joaquin', department=it_department))
s.add(Employee(name='Ramon', department=it_department))
s.add(it_department)
s.commit()

for e in s.query(Employee).all():
    print "{}".format(e.created_at)
```

