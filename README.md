import json

class Producto:
    def __init__(self, id_producto, nombre, cantidad, precio):
        self.id_producto = id_producto
        self.nombre = nombre
        self.cantidad = cantidad
        self.precio = precio

    def actualizar_cantidad(self, cantidad):
        self.cantidad = cantidad

    def actualizar_precio(self, precio):
        self.precio = precio
    
    def to_dict(self):
        return {
            "id": self.id_producto,
            "nombre": self.nombre,
            "cantidad": self.cantidad,
            "precio": self.precio
        }

class Inventario:
    def __init__(self):
        self.productos = {}
        self.cargar_desde_archivo()
    
    def agregar_producto(self, producto):
        self.productos[producto.id_producto] = producto
        self.guardar_en_archivo()
    
    def eliminar_producto(self, id_producto):
        if id_producto in self.productos:
            del self.productos[id_producto]
            self.guardar_en_archivo()
    
    def actualizar_producto(self, id_producto, cantidad=None, precio=None):
        if id_producto in self.productos:
            if cantidad is not None:
                self.productos[id_producto].actualizar_cantidad(cantidad)
            if precio is not None:
                self.productos[id_producto].actualizar_precio(precio)
            self.guardar_en_archivo()
    
    def buscar_producto(self, nombre):
        return [p.to_dict() for p in self.productos.values() if p.nombre.lower() == nombre.lower()]
    
    def mostrar_productos(self):
        return [p.to_dict() for p in self.productos.values()]
    
    def guardar_en_archivo(self):
        with open("inventario.json", "w") as f:
            json.dump([p.to_dict() for p in self.productos.values()], f)
    
    def cargar_desde_archivo(self):
        try:
            with open("inventario.json", "r") as f:
                productos_cargados = json.load(f)
                for p in productos_cargados:
                    self.productos[p["id"]] = Producto(p["id"], p["nombre"], p["cantidad"], p["precio"])
        except FileNotFoundError:
            pass

def menu():
    inventario = Inventario()
    while True:
        print("\n--- Sistema de Gestión de Inventario ---")
        print("1. Agregar producto")
        print("2. Eliminar producto")
        print("3. Actualizar producto")
        print("4. Buscar producto")
        print("5. Mostrar inventario")
        print("6. Salir")
        opcion = input("Seleccione una opción: ")
        
        if opcion == "1":
            id_producto = input("ID del producto: ")
            nombre = input("Nombre del producto: ")
            cantidad = int(input("Cantidad: "))
            precio = float(input("Precio: "))
            inventario.agregar_producto(Producto(id_producto, nombre, cantidad, precio))
        elif opcion == "2":
            id_producto = input("ID del producto a eliminar: ")
            inventario.eliminar_producto(id_producto)
        elif opcion == "3":
            id_producto = input("ID del producto a actualizar: ")
            cantidad = input("Nueva cantidad (dejar en blanco para no cambiar): ")
            precio = input("Nuevo precio (dejar en blanco para no cambiar): ")
            cantidad = int(cantidad) if cantidad else None
            precio = float(precio) if precio else None
            inventario.actualizar_producto(id_producto, cantidad, precio)
        elif opcion == "4":
            nombre = input("Nombre del producto a buscar: ")
            print(inventario.buscar_producto(nombre))
        elif opcion == "5":
            print(inventario.mostrar_productos())
        elif opcion == "6":
            break
        else:
            print("Opción no válida.")

if __name__ == "__main__":
    menu()
