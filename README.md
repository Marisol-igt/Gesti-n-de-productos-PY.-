# Gesti-n-de-productos-PY.-
import os

# Limpia la pantalla según el sistema operativo
def limpiar_pantalla():
    os.system('cls' if os.name == 'nt' else 'clear')

# Estructura de producto
def crear_producto(nombre, codigo, precio, proveedor, existencia, estado, descuento):
    return {
        "nombre": nombre, "codigo": codigo, "precio": precio, "proveedor": proveedor,
        "existencia": existencia, "estado": estado, "descuento": descuento
    }

# Agregar producto al archivo
def agregar_producto(archivo, producto):
    with open(archivo, 'a') as file:
        file.write(','.join(map(str, producto.values())) + '\n')

# Leer productos desde el archivo
def leer_productos(archivo):
    if not os.path.exists(archivo):
        return []
    with open(archivo, 'r') as file:
        return [crear_producto(*line.strip().split(',')) for line in file]

# Buscar producto por código o nombre
def buscar_producto(archivo, criterio, valor):
    productos = leer_productos(archivo)
    return [p for p in productos if (p[criterio] == valor if criterio == "codigo" else valor.lower() in p["nombre"].lower())]

# Modificar producto
def modificar_producto(archivo, codigo, nuevos_datos):
    productos = leer_productos(archivo)
    for producto in productos:
        if producto["codigo"] == codigo:
            producto.update(nuevos_datos)
    guardar_productos(archivo, productos)

# Guardar productos en el archivo
def guardar_productos(archivo, productos):
    with open(archivo, 'w') as file:
        for producto in productos:
            file.write(','.join(map(str, producto.values())) + '\n')

# Mostrar menú principal
def menu():
    archivo = "productos.txt"
    
    while True:
        limpiar_pantalla()
        print("\n--- MENÚ ---\n1. Agregar producto\n2. Buscar producto\n3. Modificar producto\n4. Salir\n")
        opcion = input("Selecciona una opción: ")

        if opcion == "1":
            limpiar_pantalla()
            nombre = input("Nombre: "); codigo = input("Código: ")
            if buscar_producto(archivo, "codigo", codigo):
                print("Error: Código duplicado.")
            else:
                producto = crear_producto(
                    nombre, codigo, float(input("Precio: ")), input("Proveedor: "), 
                    int(input("Existencia: ")), input("Estado (A/N): ").upper(), 
                    float(input("Descuento: "))
                )
                agregar_producto(archivo, producto)
                print("Producto agregado.")
            input("\nEnter para continuar...")

        elif opcion == "2":
            limpiar_pantalla()
            criterio = "codigo" if input("Buscar por código (1) o nombre (2): ") == "1" else "nombre"
            valor = input(f"Introduce {criterio}: ")
            resultados = buscar_producto(archivo, criterio, valor)
            print(f"Resultados:\n{resultados}" if resultados else "Producto no encontrado.")
            input("\nEnter para continuar...")

        elif opcion == "3":
            limpiar_pantalla()
            codigo = input("Código del producto a modificar: ")
            producto = buscar_producto(archivo, "codigo", codigo)
            if producto:
                producto = producto[0]
                nuevos_datos = {
                    "nombre": input(f"Nuevo nombre ({producto['nombre']}): ") or producto['nombre'],
                    "precio": float(input(f"Nuevo precio ({producto['precio']}): ") or producto['precio']),
                    "proveedor": input(f"Nuevo proveedor ({producto['proveedor']}): ") or producto['proveedor'],
                    "existencia": int(input(f"Nueva existencia ({producto['existencia']}): ") or producto['existencia']),
                    "estado": input(f"Nuevo estado (A/N) ({producto['estado']}): ").upper() or producto['estado'],
                    "descuento": float(input(f"Nuevo descuento ({producto['descuento']}): ") or producto['descuento'])
                }
                modificar_producto(archivo, codigo, nuevos_datos)
                print("Producto modificado.")
            else:
                print("Producto no encontrado.")
            input("\nEnter para continuar...")

        elif opcion == "4":
            limpiar_pantalla(); print("Saliendo..."); break

        else:
            print("Opción inválida."); input("\nEnter para continuar...")

# Ejecutar menú
if __name__ == "__main__":
    menu()
