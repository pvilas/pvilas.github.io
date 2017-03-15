---
layout: post
title: Ejemplo de muchos-a-muchos en SQLAlchemy
date: 2017-03-15
author: Pere Vilas
tags: SQLAlchemy
---


Este pattern es muy común en cualquier aplicación y es realmente fácil de implementar en SQLAchemy, siempre y cuando se entienda bien el **relationship** con *secondary*. Ponemos el clásico ejemplo de Departamento/Empleado, obtenido de [pythoncentral](http://pythoncentral.io/overview-sqlalchemys-expression-language-orm-queries/).

## Creación de los modelos

Un empleado puede estar asignado a más de un departamento. Representamos esta relación mediante el modelo DepartmentEmployee. Notar el uso de de *secondary* en la *relationship* y de los nombres de modelo cruzados. Usamos el plural en ambos modelos (Department, Employee) para referirnos a los empleados del departamento *employees* y a los departamentos donde trabaja el empleado *departments*.

Según lo anterior, para acceder a los empleados de un departamento (sabiendo que un mismo mismo empleado puede pertener a más de un departamento) hacemos **Department.employees**. De la misma forma, para acceder a los departamentos a los que pertenece un empleado hacemos **Employee.deparments**. 

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
    employees = relationship('Employee', secondary='department_employee')


class Employee(Base):
    __tablename__ = 'employee'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    departments = relationship('Department', secondary='department_employee')


class DepartmentEmployee(Base):
    __tablename__ = 'department_employee'
    department_id = Column(Integer, ForeignKey(
        'department.id'), primary_key=True)
    employee_id = Column(Integer, ForeignKey('employee.id'), primary_key=True)
```

Introducimos algunos datos, notar que rocio está en los departamentos de IT y de RRHH,

```python
from sqlalchemy import create_engine
engine = create_engine('sqlite:///')

from sqlalchemy.orm import sessionmaker
session = sessionmaker()
session.configure(bind=engine)
Base.metadata.create_all(engine)

s = session()
rhh = Department(name='RRHH')
s.add(rrhh)
rocio = Employee(name='Rocio')
rrhh.employees.append(rocio)
rrhh.employees.append(Employee(name='Loli'))
rrhh.employees.append(Employee(name='Lola'))

it_department = Department(name='IT')
s.add(it_department)
it_department.employees.append(Employee(name='Pere'))
it_department.employees.append(Employee(name='Antonio'))
it_department.employees.append(Employee(name='Victor'))

# na rocio està als dos departaments
it_department.employees.append(rocio)

s.add(it_department)
s.commit()
``` 

Ahora viene la parte con trampilla, para hacer las consultas con el ORM tenemos que usar la palabra **any**:

```python
print "Todos los empleados de IT:"
q = s.query(Employee).filter(
    Employee.departments.any(Department.name == 'IT')).all()
for n in q:
    print "Name: {}".format(n.name)


bname = 'Rocio'
roc = s.query(Department).filter(
    Department.employees.any(Employee.name == bname)
).all()
print "Todos los departamentos donde trabaja {}".format(bname)
for r in roc:
    print "Department: {}".format(r.name)

```


