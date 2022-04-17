# Parial-1_SistemadeInventario
Crear una aplicación de línea de comandos que permita registro, búsqueda, edición y eliminación de artículos dentro de un sistema de inventario
import sqlite3
NOMBRE_BASE_DE_DATOS = "Sistema de inventario.db"


def obtener_conexion():
    return sqlite3.connect(NOMBRE_BASE_DE_DATOS)


def crear_tablas():
    tablas = [
        """
        CREATE TABLE IF NOT EXISTS inventario(
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            articulo TEXT NOT NULL,
            seccion TEXT NOT NULL
        );
        """
    ]
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    for tabla in tablas:
        cursor.execute(tabla)


def principal():
    crear_tablas()
    menu = """
1) Agregar un nuevo articulo
2) Editar articulo existente
3) Eliminar articulo existente
4) Ver listado de articulos
5) Buscar seccion de articulo
6) Salir
Elige: """
    eleccion = ""
    while eleccion != "6":
        eleccion = input(menu)
        if eleccion == "1":
            articulo = input("Ingresa el articulo: ")
            posible_seccion = buscar_seccion_articulo(articulo)
            if posible_seccion:
                print(f"El articulo '{articulo}' ya existe")
            else:
                seccion = input("Ingresa la seccion: ")
                agregar_articulo(articulo, seccion)
                print("Articulo agregado")
        if eleccion == "2":
            articulo = input("Ingresa el articulo que quieres editar: ")
            nueva_seccion = input("Ingresa la nueva seccion: ")
            editar_articulo(articulo, nueva_seccion)
            print("Articulo actualizado")
        if eleccion == "3":
            articulo = input("Ingresa el articulo a eliminar: ")
            eliminar_articulo(articulo)
        if eleccion == "4":
            articulos = obtener_articulos()
            print("=== Lista de articulos ===")
            for articulo in articulos:
                print(articulo[0])
        if eleccion == "5":
            articulo = input(
                "Ingresa el articulo de el cual quieres saber la seccion: ")
            seccion = buscar_seccion_articulo(articulo)
            if seccion:
                print(f"El seccion de '{articulo}' es:\n{seccion[0]}")
            else:
                print(f"Articulo '{articulo}' no encontrado")


def agregar_articulo(articulo, seccion):
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    sentencia = "INSERT INTO inventario(articulo, seccion) VALUES (?, ?)"
    cursor.execute(sentencia, [articulo, seccion])
    conexion.commit()


def editar_articulo(articulo, nueva_seccion):
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    sentencia = "UPDATE inventario SET seccion = ? WHERE articulo = ?"
    cursor.execute(sentencia, [nueva_seccion, articulo])
    conexion.commit()


def eliminar_articulo(articulo):
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    sentencia = "DELETE FROM inventario WHERE articulo = ?"
    cursor.execute(sentencia, [articulo])
    conexion.commit()


def obtener_articulos():
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    consulta = "SELECT articulo FROM inventario"
    cursor.execute(consulta)
    return cursor.fetchall()


def buscar_seccion_articulo(articulo):
    conexion = obtener_conexion()
    cursor = conexion.cursor()
    consulta = "SELECT seccion FROM inventario WHERE articulo = ?"
    cursor.execute(consulta, [articulo])
    return cursor.fetchone()


if __name__ == '__main__':
    principal()
