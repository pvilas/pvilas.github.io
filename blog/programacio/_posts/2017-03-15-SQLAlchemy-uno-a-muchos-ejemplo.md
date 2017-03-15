---
layout: post
title: Ejemplo de uno-a-muchos en SQLAlchemy. Uso de join.
date: 2017-03-15
author: Pere Vilas
tags: SQLAlchemy
---
Reproducimos el clásico ejemplo de Department/Employee obtenido de [pythoncentral](http://pythoncentral.io/series/python-sqlalchemy-database-tutorial/).

Empezamos definiendo ambos modelos. **Employee** tiene una **ForeignKey** a  **Department** llamado department_id. Hasta aquí todo normal, pero seguidamente se define una relationship llamada **department** que apunta al modelo **Department**. Con este simple hecho, ya podemos acceder al departamento del empleado ya que el ORM construye y ejecuta automáticamente la consulta en el momento en que se accede a la instancia del objeto **Employee**. Se puede tener control sobre la forma y tiempo de ejecución de esta consulta mediante el parámetro *lazy* que puede buscarse en la documentación.

Notar que **department** lleva también un *backref*. Se trata de la referencia inversa desde **Department** a todos sus **Employee**. Con esta única línea también podremos acceder desde un departamento a todos sus empleados a través de **Department.employees**.

Definimos los modelos,

```python
# -*- coding: utf-8 -*-

from sqlalchemy import Column, String, Integer, ForeignKey
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
    department_id = Column(Integer, ForeignKey('department.id'))
    department = relationship(
        Department, backref=backref('employees', uselist=True))

```

Introduciomos algunos datos,

```python

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
```

Consultamos todos los empleados de IT, **via Department** usando el ORM,

```python
it = s.query(Department).filter(Department.name == 'IT').one()
print "employees: {}".format(it.employees)
print "empleat zero: {}".format(it.employees[0].name)
print "tots els empleats"
for t in it.employees:
    print "{}".format(t.name)

```

Ahora hacemos la misma consulta pero **via Employee**, notar el uso de **join** que posiblemente sea muy eficiente sobre la clave primaria de Department.

```python
find_j = s.query(Employee).join(Department)\
    .filter(Department.name == 'IT')

print "Consulta ORM via employee: {}".format(find_j)

for r in find_j.all():
    print "{}".format(r.name)

```

La consulta generada por el ORM es

```sql
SELECT employee.id AS employee_id, employee.name AS employee_name, employee.department_id AS employee_department_id
FROM employee JOIN department ON department.id = employee.department_id
WHERE department.name = ?
```

