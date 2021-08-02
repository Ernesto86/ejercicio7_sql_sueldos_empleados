# ejercicio7_sql_sueldos_empleados

Para la ejecución del código se requiere IDE básico con MYSQL >= 5.7

**1 CRAEAR LA BASE DE DATOS**
CREATE DATABASE enviame
    CHARACTER SET 'utf8'
    COLLATE 'utf8_general_ci';

use enviame


**2 CRAEAR LAS TABLAS**

CREATE TABLE countries ( 
	id int(10) unsigned NOT NULL AUTO_INCREMENT, 
	continent_id int(11) NOT NULL, 
	name varchar(25) NOT NULL, 
	PRIMARY KEY (id) 
);

CREATE TABLE continents ( 
	id int(10) unsigned NOT NULL AUTO_INCREMENT, 
	name varchar(25) NOT NULL, 
	anual_adjustment int(11) NOT NULL, 
	PRIMARY KEY (id) 
);

CREATE TABLE employees ( 
	id int(10) unsigned NOT NULL AUTO_INCREMENT, 
	country_id int(11) NOT NULL, 
	first_name varchar(25) NOT NULL, 
	last_name varchar(25) NOT NULL, 
	salary int(11) NOT NULL, 
	PRIMARY KEY (id) 
);


**3 INSERTAMOS LOS REGISTROS**

INSERT INTO continents VALUES (NULL, 'América', 4);
INSERT INTO continents VALUES (NULL, 'Europa', 5);
INSERT INTO continents VALUES (NULL, 'Asia', 6);
INSERT INTO continents VALUES (NULL, 'Oceanía', 6);
INSERT INTO continents VALUES (NULL, 'Africa', 5);
INSERT INTO countries VALUES (NULL, 1, 'Chile');
INSERT INTO countries VALUES (NULL, 1, 'Argentina');
INSERT INTO countries VALUES (NULL, 1, 'Canadá');
INSERT INTO countries VALUES (NULL, 1, 'Colombia');
INSERT INTO countries VALUES (NULL, 2, 'Alemania');
INSERT INTO countries VALUES (NULL, 2, 'Francia');
INSERT INTO countries VALUES (NULL, 2, 'España');
INSERT INTO countries VALUES (NULL, 2, 'Grecia');
INSERT INTO countries VALUES (NULL, 3, 'India');
INSERT INTO countries VALUES (NULL, 3, 'Japón');
INSERT INTO countries VALUES (NULL, 3, 'Corea del Sur');
INSERT INTO countries VALUES (NULL, 4, 'Australia');
INSERT INTO employees VALUES (NULL, 1, 'Pedro', 'Rojas', 2000);
INSERT INTO employees VALUES (NULL, 2, 'Luciano', 'Alessandri', 2100);
INSERT INTO employees VALUES (NULL, 3, 'John', 'Carter', 3050);
INSERT INTO employees VALUES (NULL, 4, 'Alejandra', 'Benavides', 2150);
INSERT INTO employees VALUES (NULL, 5, 'Moritz', 'Baring', 6000);
INSERT INTO employees VALUES (NULL, 6, 'Thierry', 'Henry', 5900);
INSERT INTO employees VALUES (NULL, 7, 'Sergio', 'Ramos', 6200);
INSERT INTO employees VALUES (NULL, 8, 'Nikoleta', 'Kyriakopulu', 7000);
INSERT INTO employees VALUES (NULL, 9, 'Aamir', 'Khan', 2000);
INSERT INTO employees VALUES (NULL, 10, 'Takumi', 'Fujiwara', 5000);
INSERT INTO employees VALUES (NULL, 11, 'Heung-min', 'Son', 5100);
INSERT INTO employees VALUES (NULL, 12, 'Peter', 'Johnson', 6100);



**3 CREAMOS UN CURSOR PARA ACTUALIZAR LOS SALARIOS DE LOS EMPLEADOS**

DELIMITER //
DROP PROCEDURE IF EXISTS getUpdateSalary //
CREATE PROCEDURE getUpdateSalary()
BEGIN
  DECLARE var_id INTEGER;
  DECLARE var_salary INTEGER;
  DECLARE var_anual_adjustment INTEGER;
  DECLARE var_adjustment_salary INTEGER DEFAULT 0;
  DECLARE var_final INTEGER DEFAULT 0;


  DECLARE cursor_salary CURSOR FOR 
  
   SELECT employe.id,employe.salary, continent.anual_adjustment  FROM employees employe
	INNER JOIN countries country ON employe.country_id=country.id
	INNER JOIN continents continent ON country.continent_id=continent.id;

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET var_final = 1;

  OPEN cursor_salary;

  bucle: LOOP

    FETCH cursor_salary INTO var_id, var_salary, var_anual_adjustment;

    IF var_final = 1 THEN
      LEAVE bucle;
    END IF;
    
    IF var_salary <=5000 THEN
      SET var_adjustment_salary = ( (var_salary * var_anual_adjustment)/100) + var_salary;
    
      UPDATE employees SET salary = var_adjustment_salary WHERE id = var_id;
      
      /*SELECT *  FROM employees WHERE id = var_id;*/      
      
    END IF;    

  END LOOP bucle;  
  
  CLOSE cursor_salary;

END//
DELIMITER ;

**4 EJECUTAMOS EL PROCEDIMIENTO ALMACENADO**

call getUpdateSalary();


**5 RESULTADO FINAL **

SELECT employe.id, employe.first_name,employe.last_name, employe.salary, continent.anual_adjustment, continent.name, country.name  FROM employees employe
INNER JOIN countries country ON employe.country_id=country.id
INNER JOIN continents continent ON country.continent_id=continent.id
