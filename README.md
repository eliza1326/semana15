# semana15
desarrollo de un programa con actividades especificas como ingreso, eliminacion de un cliente, y ademas con el uso en un local de venta de quimicos y envases.

import tkinter as tk
from tkinter import messagebox, simpledialog


class Cliente:
    def __init__(self, nombre):
        self.nombre = nombre
        self.pedidos = []

    def agregar_pedido(self, pedido):
        self.pedidos.append(pedido)

    def eliminar_pedido(self, pedido_id):
        self.pedidos = [pedido for pedido in self.pedidos if pedido.id_pedido != pedido_id]

    def listar_pedidos(self):
        return [f"Pedido {pedido.id_pedido}: {', '.join(pedido.productos)} - {'Completado' if pedido.completado else 'Pendiente'}" for pedido in self.pedidos]


class Pedido:
    id_counter = 1

    def __init__(self, productos):
        self.id_pedido = Pedido.id_counter
        Pedido.id_counter += 1
        self.productos = productos
        self.completado = False

    def marcar_como_completado(self):
        self.completado = True


class SistemaGestionApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Gestión de Clientes y Pedidos")
        self.root.geometry("600x400")

        self.clientes = {}

        # Frame para entrada de clientes
        self.frame_cliente = tk.Frame(self.root)
        self.frame_cliente.pack(pady=10)

        self.entry_cliente = tk.Entry(self.frame_cliente, width=35)
        self.entry_cliente.pack(side=tk.LEFT, padx=10)

        self.button_agregar_cliente = tk.Button(self.frame_cliente, text="Agregar Cliente", command=self.agregar_cliente)
        self.button_agregar_cliente.pack(side=tk.LEFT)

        # Lista de clientes
        self.listbox_clientes = tk.Listbox(self.root, height=10, width=50)
        self.listbox_clientes.pack(pady=10)

        # Botones para gestionar clientes
        self.frame_acciones = tk.Frame(self.root)
        self.frame_acciones.pack(pady=10)

        self.button_eliminar_cliente = tk.Button(self.frame_acciones, text="Eliminar Cliente", command=self.eliminar_cliente)
        self.button_eliminar_cliente.pack(side=tk.LEFT, padx=10)

        self.button_agregar_pedido = tk.Button(self.frame_acciones, text="Añadir Pedido", command=self.agregar_pedido)
        self.button_agregar_pedido.pack(side=tk.LEFT, padx=10)

        self.button_completar_pedido = tk.Button(self.frame_acciones, text="Completar Pedido", command=self.completar_pedido)
        self.button_completar_pedido.pack(side=tk.LEFT, padx=10)

    # Función para agregar cliente
    def agregar_cliente(self):
        nombre = self.entry_cliente.get()
        if nombre:
            if nombre not in self.clientes:
                self.clientes[nombre] = Cliente(nombre)
                self.listbox_clientes.insert(tk.END, nombre)
                self.entry_cliente.delete(0, tk.END)
            else:
                messagebox.showwarning("Advertencia", f"El cliente '{nombre}' ya existe.")
        else:
            messagebox.showwarning("Advertencia", "El nombre del cliente no puede estar vacío.")

    # Función para eliminar cliente
    def eliminar_cliente(self):
        try:
            seleccionado = self.listbox_clientes.curselection()[0]
            nombre_cliente = self.listbox_clientes.get(seleccionado)
            del self.clientes[nombre_cliente]
            self.listbox_clientes.delete(seleccionado)
        except IndexError:
            messagebox.showwarning("Advertencia", "Selecciona un cliente para eliminar.")

    # Función para agregar pedido
    def agregar_pedido(self):
        try:
            seleccionado = self.listbox_clientes.curselection()[0]
            nombre_cliente = self.listbox_clientes.get(seleccionado)
            cliente = self.clientes[nombre_cliente]
            productos = simpledialog.askstring("Agregar Pedido", "Ingrese productos separados por coma:")
            if productos:
                pedido = Pedido(productos.split(","))
                cliente.agregar_pedido(pedido)
                messagebox.showinfo("Éxito", f"Pedido añadido para {nombre_cliente}.")
        except IndexError:
            messagebox.showwarning("Advertencia", "Selecciona un cliente para agregar el pedido.")

    # Función para completar pedido
    def completar_pedido(self):
        try:
            seleccionado = self.listbox_clientes.curselection()[0]
            nombre_cliente = self.listbox_clientes.get(seleccionado)
            cliente = self.clientes[nombre_cliente]
            pedidos = cliente.listar_pedidos()
            if pedidos:
                pedido_id = simpledialog.askinteger("Completar Pedido", "Ingrese ID del pedido a completar:")
                for pedido in cliente.pedidos:
                    if pedido.id_pedido == pedido_id:
                        pedido.marcar_como_completado()
                        messagebox.showinfo("Éxito", f"Pedido {pedido_id} completado.")
                        return
                messagebox.showwarning("Advertencia", f"No se encontró el pedido con ID {pedido_id}.")
            else:
                messagebox.showwarning("Advertencia", "Este cliente no tiene pedidos.")
        except IndexError:
            messagebox.showwarning("Advertencia", "Selecciona un cliente primero.")

# Configuración de la ventana principal
if __name__ == "__main__":
    root = tk.Tk()
    app = SistemaGestionApp(root)
    root.mainloop()
