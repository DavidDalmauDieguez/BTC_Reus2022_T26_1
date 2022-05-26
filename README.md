<table>
<tr>
<td width="100px"><img src="https://user-images.githubusercontent.com/103035621/170483040-a88d598b-145b-4903-accb-948ceff05811.png" alt="Team DOU"/></td>
<td width="1100px"> <h2>MSKA: Spring + JPA + MYSQL + Spring Security UD26_Ejercicio-1</h2> </td>

</tr>
</table>

[![Java](https://img.shields.io/badge/Java-FrontEnd-informational)]()
[![GitHub](https://img.shields.io/badge/GitHub-Repository-lightgrey)]()
[![SQL](https://img.shields.io/badge/SQL-DataBase-yellowgreen)]()
[![Spring](https://img.shields.io/badge/Spring-infrastructure-brightgreen)]()
[![Maven](https://img.shields.io/badge/Maven-ProjectStructure-blueviolet)]()

Este ejercicio ha sido realizado por los miembros del equipo 1. Dicho equipo esta formado por:

[- Ixabel Justo Etxeberria](https://github.com/Kay-Nicte)<br>
[- J.Oriol López Bosch](https://github.com/mednologic)<br>
[- Octavio Bernal](https://github.com/OctavioBernalGH)<br>
[- David Dalmau](https://github.com/DavidDalmauDieguez)

<p align="justify">Se crea un proyecto Maven utilizando la tecnología spring, se definen como componentes los spring services, la base de datos H2 y JPA. Se crea la estructura de proyecto en capas definiendo los paquetes de controllers, dao, dto y services. Para proseguir se crean las entidades 'piezas', 'proveedores',  y 'suministra' con una relación de uno a muchos (one to many). Se definen las columnas y mediante anotaciones se mapea con los atributos de la entidad.</p>

A continuación se expondrá el desarrollo completo de la aplicación. A la hora de comenzar con un proyecto Spring es muy importante configurar el fichero application.propierties ubicado en la carpeta de resources. En este caso la configuración queda tal que así:

<p align="center">
  <img src="https://user-images.githubusercontent.com/103035621/170081139-436a265f-4dd0-4fe3-b1d8-379d39ab10c5.png">
</p>

El siguiente paso corresponde a la definición de la base de datos según el esquema entidad relación, para ello se ha creado un script de creación de base de datos con varias tuplas de datos de prueba. El fichero es denominado data.sql y está ubicado en la misma carpeta que el application.propierties. A continuación se puede observar el código generado:

```sql
drop database if exists UD26_Ejercicio_1;
create database if not exists UD26_Ejercicio_1;

use UD26_Ejercicio_1;

create table piezas
(
	id int auto_increment,
    nombre varchar(100),
    primary key (id)    
);

insert into piezas (nombre) values ('tornillo');
insert into piezas (nombre) values ('tuerca');
insert into piezas (nombre) values ('martillo');
insert into piezas (nombre) values ('puerta');
insert into piezas (nombre) values ('lechuga');

create table proveedores
(
	id int auto_increment,
    nombre varchar(100),
	primary key (id)
);

insert into proveedores (nombre) values ('Paco vendedor');
insert into proveedores (nombre) values ('Manolo vendedor');
insert into proveedores (nombre) values ('Maria vendedor');
insert into proveedores (nombre) values ('Cristina vendedor');
insert into proveedores (nombre) values ('Manuela vendedor');

create table suministra
(
	id int auto_increment,
	codigo_pieza int,
    id_proveedor int,
    precio int,
    primary key (id),
    foreign key (codigo_pieza) references piezas(id),
    foreign key (id_proveedor) references proveedores(id)
);

insert into suministra (codigo_pieza, id_proveedor, precio) values (1, 1, 150);
insert into suministra (codigo_pieza, id_proveedor, precio) values (2, 2, 150);
insert into suministra (codigo_pieza, id_proveedor, precio) values (3, 3, 150);
insert into suministra (codigo_pieza, id_proveedor, precio) values (4, 4, 150);
insert into suministra (codigo_pieza, id_proveedor, precio) values (5, 5, 150);
```

El siguiente paso será crear las diferentes clases, cada clase representa una tabla y los atributos de cada clase representan las columnas de la base de datos, de esta manera mediante las diferentes anotaciones creamos un mapeo entre objeto-tabla.

Las principales anotaciones a la hora de mapear una clase con la tabla son:

@Entity --> Se va a crear una entidad.<br>
@Table(name = "nombre de la tabla") --> El nombre de la tabla corresponde a...<br>
@Id --> El siguiente atributo será la clave primaria.<br>
@GeneratedValue(strategy = GeneratedType.IDENTITY) --> La clave primaria se generará de forma auto incremental.<br>
@Column(name = "nombre_col") --> El nombre de la columna corresponde al atributo de abajo.<br>
private TipoVariable nombreCol;

A continuación se dejan tres spoilers con el código generado en las clases.

<details>
  
<summary>Código entidad Piezas</summary>
  
<br>
  
```java
package com.example.demo.dto;

import java.util.List;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToMany;
import javax.persistence.Table;

import com.fasterxml.jackson.annotation.JsonIgnore;

@Entity
@Table(name="piezas")
public class Piezas {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)//busca ultimo valor y lo incrementa
	private Long id;
	
	@Column(name="nombre")
	private String nombre;

	// One to many
	@OneToMany
	@JoinColumn(name="id")
	private List<Suministra> suministra;

	//Constructores
	public Piezas() {

	}

	public Piezas(Long id, String nombre, List<Suministra> suministra) {
		this.id = id;
		this.nombre = nombre;
		this.suministra = suministra;
	}

	public Long getCodigo() {
		return id;
	}

	public void setCodigo(Long id) {
		this.id = id;
	}

	public String getNombre() {
		return nombre;
	}

	public void setNombre(String nombre) {
		this.nombre = nombre;
	}

	public void setSuministra(List<Suministra> suministra) {
		this.suministra = suministra;
	}
	
	@JsonIgnore
	@OneToMany(fetch = FetchType.LAZY, mappedBy = "Suministra")
	public List<Suministra> getSuministra() {
		return suministra;
	}
	
	@Override
	public String toString() {
		return "Piezas [codigo=" + id + ", nombre=" + nombre + ", suministra=" + suministra + "]";
	} 
}
  
```
  
</details>




<details>
  
<summary>Código entidad Proveedores</summary>
  
<br>
  
```java
package com.example.demo.dto;

import java.util.List;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToMany;
import javax.persistence.Table;

import com.fasterxml.jackson.annotation.JsonIgnore;

@Entity
@Table(name="proveedores")
public class Proveedores {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)//busca ultimo valor y lo incrementa
	private Long id;

	@Column(name="nombre")
	private String nombre;

	// One to many
	@OneToMany
	@JoinColumn(name="id")
	private List<Suministra> suministra;
	
	//Constructores
	public Proveedores () {
		
	}

	public Proveedores(Long id, String nombre, List<Suministra> suministra) {
		super();
		this.id = id;
		this.nombre = nombre;
		this.suministra = suministra;
	}

	// Getters y setters
	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getNombre() {
		return nombre;
	}

	public void setNombre(String nombre) {
		this.nombre = nombre;
	}

	@JsonIgnore
	@OneToMany(fetch = FetchType.LAZY, mappedBy = "Suministra")
	public List<Suministra> getSuministra() {
		return suministra;
	} 

	public void setSuministra(List<Suministra> suministra) {
		this.suministra = suministra;
	}

	
}
  
```
  
</details>




<details>
  
<summary>Código entidad Suministra</summary>
  
<br>
  
```java
package com.example.demo.dto;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.Table;

@Entity
@Table(name="suministra")
public class Suministra {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	
	@ManyToOne
	@JoinColumn(name="codigo_pieza")
	private Piezas piezas;
	
	@ManyToOne
	@JoinColumn(name="id_proveedor")
	private Proveedores proveedores;
	
	@Column(name="precio")
	private int precio;
	
	//Constructores
	public Suministra() {
		
	}

	/**
	 * @param id
	 * @param codigoPieza
	 * @param idProveedor
	 * @param precio
	 */
	public Suministra(Long id, Piezas piezas, Proveedores proveedores, int precio) {
		super();
		this.id = id;
		this.piezas = piezas;
		this.proveedores = proveedores;
		this.precio = precio;
	}

	/**
	 * @return the id
	 */
	public Long getId() {
		return id;
	}

	/**
	 * @param id the id to set
	 */
	public void setId(Long id) {
		this.id = id;
	}

	/**
	 * @return the piezas
	 */
	public Piezas getPiezas() {
		return piezas;
	}

	/**
	 * @param piezas the piezas to set
	 */
	public void setPiezas(Piezas piezas) {
		this.piezas = piezas;
	}

	/**
	 * @return the proveedores
	 */
	public Proveedores getProveedores() {
		return proveedores;
	}

	/**
	 * @param proveedores the proveedores to set
	 */
	public void setProveedores(Proveedores proveedores) {
		this.proveedores = proveedores;
	}

	/**
	 * @return the precio
	 */
	public int getPrecio() {
		return precio;
	}

	/**
	 * @param precio the precio to set
	 */
	public void setPrecio(int precio) {
		this.precio = precio;
	}

}  
```
  
</details>
	
Una vez definidas las entidades del paquete DTO se procederá a la creación de las interfaces DAO, dichas interfaces heredan los métodos CRUD básicos del repositorio Jpa, para ello las tres interfaces extenderan JpaRepository. Jpa proporciona un modelo de datos genérico referente al CRUD. También se definira la anotación @Repository para indicar el acceso a la base de datos. En el siguiente cuadro de texto se muestra el código generado en los diferentes DAO , se ubica todo en este cuadro debido a la escaséz de código.
	
```java
	
package com.example.demo.dao;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.dto.Piezas;

@Repository
//Repositorio de funcion es de base de datos
public interface PiezasDAO extends JpaRepository<Piezas,Long>{

}

package com.example.demo.dao;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.dto.Proveedores;

@Repository
//Repositorio de funcion es de base de datos
public interface ProveedoresDAO extends JpaRepository<Proveedores,Long>{

}

package com.example.demo.dao;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.dto.Suministra;

@Repository
//Repositorio de funcion es de base de datos
public interface SuministraDAO extends JpaRepository<Suministra,Long>{

}

```

Se proseguirá creando las interfaces de los métodos que se utilizarán en la capa service, en este caso serán 3 interfaces, la de piezas, proveedores y suministra. En dichas interfaces se generará la cabecera de los métodos que implementarán las diferentes clases de la capa service y que posteriormente se desarrollarán y mapearán en los controladores, a continuación se introduce el código generado para las interfaces en desplegables.
	
<details>
	
<summary>Interface Piezas</summary>
	
<br>
	
```java
	
package com.example.demo.service;

import java.util.List;

import com.example.demo.dto.Piezas;


public interface PiezasService {
	public List<Piezas> listarPiezas();
	
	public Piezas crearPiezas(Piezas piezas);

	public Piezas modificarPiezas(Piezas piezas);

	public void eliminarPiezas(Long id);

	public Piezas buscarPiezas(Long id);
	
}
	
```
	
</details>
	
	
<details>
	
<summary>Interface Proveedores</summary>
	
<br>
	
```java
	
package com.example.demo.service;

import java.util.List;

import com.example.demo.dto.Proveedores;

public interface ProveedoresService {
public List<Proveedores> listarProveedores();
	
	public Proveedores crearProveedores(Proveedores proveedores);

	public Proveedores modificarProveedores(Proveedores proveedores);

	public void eliminarProveedores(Long id);

	public Proveedores buscarProveedores(Long id);

}
	
```
	
</details>
	
	
<details>
	
<summary>Interfaz Suministra</summary>
	
<br>
	
```java
	
package com.example.demo.service;

import java.util.List;


import com.example.demo.dto.Suministra;

public interface SuministraService {
	public List<Suministra> listarSuministra();

	public Suministra crearSuministra(Suministra suministra);

	public Suministra modificarSuministra(Suministra suministra);

	public void eliminarSuministra(Long id);

	public Suministra buscarSuministra(Long id);
}
	
```
	
</details>
	
Completadas ya las interfaces se procederá a la creación de las clases que las implementarán. Una vez credas se definirá la anotación @Service indicando que esta clase pertenece a la capa de servicios y la anotación @Autowired que inyectará las dependencias del Jpa heredadas del dao. Una vez definido el 'implements' en la cabecera de la clase, eclipse pedirá aplicar los métodos de las interfaces.
	
Se rellenará el cuerpo de los métodos con los recibidos por Jpa, Jpa tiene funciones própias de CRUD, a continuación hay tres desplegables con el código generado en las diferentes clases de la capa service.
	
<details>
	
<summary>Clase Service de Piezas</summary>

<br>

```java
	
package com.example.demo.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.example.demo.dao.PiezasDAO;
import com.example.demo.dto.Piezas;

@Service
public class PiezasServiceImpl implements PiezasService{
	// Utilizar metodos DAO heredados de JPA
	@Autowired
	PiezasDAO piezasDao;

	@Override
	public List<Piezas> listarPiezas() {
		return piezasDao.findAll();
	}

	@Override
	public Piezas crearPiezas(Piezas piezas) {
		return piezasDao.save(piezas);
	}

	@Override
	public Piezas modificarPiezas(Piezas piezas) {
		return piezasDao.save(piezas);
	}

	@Override
	public void eliminarPiezas(Long id) {
		piezasDao.deleteById(id);
		
	}

	@Override
	public Piezas buscarPiezas(Long id) {
		return piezasDao.findById(id).get();
	}
	
	
}
```
	
</details>
	
	
<details>
	
<summary>Clase Service de Proveedores</summary>

<br>

```java

package com.example.demo.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.example.demo.dao.ProveedoresDAO;
import com.example.demo.dto.Proveedores;

@Service
public class ProveedoresServiceImpl implements ProveedoresService{
	// Utilizar metodos DAO heredados de JPA
	@Autowired
	ProveedoresDAO proveedoresDao;

	@Override
	public List<Proveedores> listarProveedores() {
		return proveedoresDao.findAll();
	}

	@Override
	public Proveedores crearProveedores(Proveedores proveedores) {
		return proveedoresDao.save(proveedores);
	}

	@Override
	public Proveedores modificarProveedores(Proveedores proveedores) {
		return proveedoresDao.save(proveedores);
	}

	@Override
	public void eliminarProveedores(Long id) {
		proveedoresDao.deleteById(id);
		
	}

	@Override
	public Proveedores buscarProveedores(Long id) {
		return proveedoresDao.findById(id).get();
	}	
}
	
```
	
</details>
	
	
<details>
	
<summary>Clase Service de Suministra</summary>

<br>

```java

package com.example.demo.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.example.demo.dao.SuministraDAO;
import com.example.demo.dto.Suministra;

@Service
public class SuministraServiceImpl implements SuministraService{
	// Utilizar metodos DAO heredados de JPA
	@Autowired
	SuministraDAO suministraDao;

	@Override
	public List<Suministra> listarSuministra() {
		return suministraDao.findAll();
	}

	@Override
	public Suministra crearSuministra(Suministra suministra) {
		return suministraDao.save(suministra);
	}

	@Override
	public Suministra modificarSuministra(Suministra suministra) {
		return suministraDao.save(suministra);
	}

	@Override
	public void eliminarSuministra(Long id) {
		suministraDao.deleteById(id);
		
	}

	@Override
	public Suministra buscarSuministra(Long id) {
		return suministraDao.findById(id).get();
	}
}
	
```
	
</details>
	
Para finalizar la parte java faltará crear los controladores, para ello en la capa controllers se definen los controladores piezas, proveedores y suministra. En la clase controller se tendrán que añadir las anotaciones @RestController para indicarle a spring que este controlador es del tipo rest, @RequestMapping("/api") para la raíz de la aplicación referente a los endpoints, la anotación @Autowired para inyectar las dependencias de la capa service definidos e implementados sobre la clase 'PiezasServiceImpl', 'ProveedoresServiceImpl' y 'SuministraServiceImpl'.
	
Para finalizar con la explicación de las anotaciones se utilizarán los mapeos de método HTTP, dichas anotaciones son:
	
@GetMapping("/ruta api") --> Método utilizado para obtener datos.<br>
@PostMapping("/ruta api") --> Método utilizado para crear algún tipo de registro.<br>
@PutMapping("/ruta api") --> Método para actualizar registros.<br>
@DeleteMapping("/ruta api") --> Método para eliminar un registro.<br>
	
A continuación se generan los desplegables que hacen referencia a los controladores creados:
	
<details>
	
<summary>Piezas Controller</summary>
	
<br>
	
```java
	
package com.example.demo.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.dto.Piezas;
import com.example.demo.service.PiezasServiceImpl;

@RestController
@RequestMapping("/api") //Raiz de la app
public class PiezasController {
	@Autowired
	PiezasServiceImpl piezasServiceImpl;

	// Listar todas las piezas
	@GetMapping("/piezas")
	public List<Piezas> listarPiezas(){
		return piezasServiceImpl.listarPiezas();
	}

	// Buscar las piezas por id
	@GetMapping("/piezas/{id}")
	public Piezas buscarPiezaCodigo(@PathVariable(name= "id")Long id) {
		return piezasServiceImpl.buscarPiezas(id);		
	}

	// Eliminar una piezas
	@DeleteMapping("/piezas/{id}")
	public void eliminarPiezas(@PathVariable(name="id")Long id) {
		piezasServiceImpl.eliminarPiezas(id);
		System.out.println("Se ha eliminado la pieza "+ id + " statisfactoriamente");
	}

	// Crear piezas
	@PostMapping("/piezas")
	public Piezas crearPeliculas(@RequestBody Piezas piezas) {
		return piezasServiceImpl.crearPiezas(piezas);
	}

	// Modificar sala
	@PutMapping("/piezas/{id}")
	public Piezas modificarPiezas (@PathVariable(name="id")Long id, @RequestBody Piezas piezas) {
		Piezas pieza_a_modificar = new Piezas();
		Piezas modificado = new Piezas();

		// Busco el id de la sala que quiero cambiar
		pieza_a_modificar = piezasServiceImpl.buscarPiezas(id);
		pieza_a_modificar.setCodigo(piezas.getCodigo());
		pieza_a_modificar.setNombre(piezas.getNombre());
		pieza_a_modificar.setSuministra(piezas.getSuministra());

		// Modificado es = a los cambios aplicados
		modificado = piezasServiceImpl.modificarPiezas(pieza_a_modificar);

		return modificado;
	}
}
	
```
	
</details>
	
	
<details>
	
<summary>Proveedores Controller</summary>
	
<br>
	
```java
	
package com.example.demo.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.dto.Proveedores;
import com.example.demo.service.ProveedoresServiceImpl;

@RestController
@RequestMapping("/api") //Raiz de la app
public class ProveedoresController {
	@Autowired
	ProveedoresServiceImpl proveedoresServiceImpl;

	// Listar todas las proveedores
	@GetMapping("/proveedores")
	public List<Proveedores> listarProveedores(){
		return proveedoresServiceImpl.listarProveedores();
	}

	// Buscar las proveedores por id
	@GetMapping("/proveedores/{id}")
	public Proveedores buscarProveedorCodigo(@PathVariable(name= "id")Long id) {
		return proveedoresServiceImpl.buscarProveedores(id);		
	}

	// Eliminar una proveedores
	@DeleteMapping("/proveedores/{id}")
	public void eliminarProveedores(@PathVariable(name="id")Long id) {
		proveedoresServiceImpl.eliminarProveedores(id);
		System.out.println("Se ha eliminado el proveedor "+ id + " statisfactoriamente");
	}

	// Crear piezas
	@PostMapping("/proveedores")
	public Proveedores crearproveedores(@RequestBody Proveedores proveedores) {
		return proveedoresServiceImpl.crearProveedores(proveedores);
	}

	// Modificar sala
	@PutMapping("/proveedores/{id}")
	public Proveedores modificarProveedores (@PathVariable(name="id")Long id, @RequestBody Proveedores proveedores) {
		Proveedores proveedores_a_modificar = new Proveedores();
		Proveedores modificado = new Proveedores();

		// Busco el id de la sala que quiero cambiar
		proveedores_a_modificar = proveedoresServiceImpl.buscarProveedores(id);

		proveedores_a_modificar.setId(proveedores.getId());
		proveedores_a_modificar.setNombre(proveedores.getNombre());

		// Modificado es = a los cambios aplicados
		modificado = proveedoresServiceImpl.modificarProveedores(proveedores_a_modificar);

		return modificado;
	}
}
	
```
	
</details>
	
	
<details>
	
<summary>Suministra Controller</summary>
	
<br>
	
```java
	
package com.example.demo.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.dto.Proveedores;
import com.example.demo.dto.Suministra;
import com.example.demo.service.SuministraServiceImpl;

@RestController
@RequestMapping("/api") //Raiz de la app
public class SuministraController {
	@Autowired
	SuministraServiceImpl suministraServiceImpl;

	// Listar todas las suministra
	@GetMapping("/suministra")
	public List<Suministra> listarProveedores(){
		return suministraServiceImpl.listarSuministra();
	}


	// Buscar las proveedores por id
	@GetMapping("/suministra/{id}")
	public Suministra buscarSuministraCodigo(@PathVariable(name= "id") Long id) {
		return suministraServiceImpl.buscarSuministra(id);		
	}	

	// Eliminar una proveedores
	@DeleteMapping("/suministra/{id}")
	public void eliminarSuministra(@PathVariable(name="id")Long id) {
		suministraServiceImpl.eliminarSuministra(id);
		System.out.println("Se ha eliminado el suministro "+ id + " statisfactoriamente");
	}

	// Crear piezas
	@PostMapping("/suministra")
	public Suministra crearSuministra(@RequestBody Suministra suministra) {
		return suministraServiceImpl.crearSuministra(suministra);
	}

	// Modificar sala
	@PutMapping("/suministra/{id}")
	public Suministra modificarSuministra (@PathVariable(name="id")Long id, @RequestBody Suministra suministra) {
		Suministra suministra_a_modificar = new Suministra();
		Suministra modificado = new Suministra();

		// Busco el id de la sala que quiero cambiar
		suministra_a_modificar = suministraServiceImpl.buscarSuministra(id);
		suministra_a_modificar.setId(suministra.getId());
		suministra_a_modificar.setPiezas(suministra.getPiezas());
		suministra_a_modificar.setProveedores(suministra.getProveedores());
		suministra_a_modificar.setPrecio(suministra.getPrecio());

		// Modificado es = a los cambios aplicados
		modificado = suministraServiceImpl.modificarSuministra(suministra_a_modificar);

		return modificado;
	}
}	
```
	
</details>

A estas alturas lo único que quedará será verificar el funcionamiento del aplicativo, para ello se utilizará postman para testear los endpoints de cada entidad. Lo primero de todo será proceder con la comprobación de obtener todos los datos de piezas, proveedor y suministra, para ello se utilizará el método HTTP GET apuntando a la dirección del controlador referente a listar. En las siguientes tres imagenes se muestra el resultado:

<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170089654-f9063139-be58-470e-884b-391d3e40b3e1.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170089662-43dc2b2a-47b3-4eea-b712-e66a2fcc33a6.PNG">
</p>

<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170089666-d422768d-cf6d-4136-8fe2-44fe7d909fc2.PNG">
</p>

La siguiente verificación pasará por el endpoint de buscar un componente por identificador, para ello se utilizará el método HTTP GET apuntando a la dirección del controlador referente a listar. En las siguientes tres imagenes se muestra el resultado:
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170090450-44d94fc8-d193-4d9d-9359-67ce440b0d67.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170090485-5a5efd62-50a2-4377-9c7b-be92a52d9f1d.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170090552-0b349b3a-f1e9-423a-812e-075597dbcdef.PNG">
</p>
	
El siguiente procedimiento de verificación constará de apuntar al endpoint de modificación de datos de entidad para ello se utilizará el método HTTP PUT indicando en la URI del endpoint el identificador del componentes y introduciendo en el body los datos a modificar en JSON. Se muestra el ejemplo en las siguientes capturas de pantalla:
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170090843-8b444ee2-29ca-4183-9b6d-11fb32f77edf.PNG">
</p>

<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170090988-51d20663-23cc-4781-b7ce-c1be7823e4df.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091034-e4de542d-b248-4a36-9c57-23fa42b6b1ed.PNG">
</p>

A continuación se verificará la eliminación de registros de las diferentes tablas, para ello se utilizará el método HTTP DELETE apuntando al endpoint correspondiente, en este caso también se pedirá al usuario que introduzca el identificador del componente a eliminar, al ser de tipo void no mostrará nada por pantalla, para mostrar correctamente la eliminación se muestra en consola un mensaje. Se puede visualizar el ejemplo en las diferentes capturas:
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091387-04e19388-2a0a-4249-942d-6ee02c02f1f4.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091427-1d39bf6a-a1ae-4ace-85a8-e62591cd15f5.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091477-7e3c65c2-3029-4538-8c7d-dc45fb9b6929.PNG">
</p>
	
Por último verificar la creación de nuevos elementos en las diferentes tablas, para ello se utiliza el método HTTP POST seguido de la ruta del endpoint, se ha de especificar en el body los datos del nuevo componente a crear. En las siguientes capturas se observa un ejemplo:
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091732-9b2385e6-8154-4703-8bfc-dd51b4a83fad.PNG">
</p>

<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091788-890e2747-61a3-4113-9c28-b712dfc6b9ec.PNG">
</p>
	
<p align="center">
	<img src="https://user-images.githubusercontent.com/103035621/170091849-055e6789-098a-4070-b55c-c9d51198604c.PNG">
</p>
	
La actividad se da por finalizada, como observación es muy importante el uso de las anotaciones, la estructura de proyecto y la correcta nomenclatura puesto que un error en la tipografía puede provocar que no funciones los endpoints.
