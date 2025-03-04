import random  # Importa la librería para generar números aleatorios
import os  # Importa la librería para limpiar la consola

FICHAS_POR_JUGADOR = 4  # Número de fichas que tiene cada jugador


class Dados:
    """Clase que representa los dados del juego."""

    def __init__(self):
        self.num_dados = 2  # Se usan dos dados en cada turno

    def lanzar_dados(self):
        """Genera dos números aleatorios entre 1 y 6 simulando el lanzamiento de dados."""
        dado1 = random.randint(1, 6)
        dado2 = random.randint(1, 6)
        return dado1, dado2

    def es_cinco(self, dado1, dado2):
        """Verifica si alguno de los dados es 5 o si la suma de ambos es 5."""
        return (dado1 + dado2 == 5) or (dado1 == 5) or (dado2 == 5)


class Tablero:
    """Clase que representa el tablero del juego."""

    def __init__(self):
        # Representación en ASCII del tablero con posiciones fijas de casillas y fichas
        self.diseno = [
            ["*****", "*****", "*****", "*****", "*****", "*****", "*****", "____|", "_s__|", "____|", "*****", "*****", "*****", "*****", "*****", "*****", "*****"],
            ["*****", "*****", "*****", "*****", "*****", "*****", "*****", "____|", "*****", "____|", "*****", "*****", "*****", "*****", "*****", "*****", "*****"],
            ["*****", "*****", " b1 |", "*****", " b2 |", "*****", "*****", "____|", "*****", "____|", "*****", "*****", " a3 |", "*****", " a1 |", "*****", "*****"],
            # (Más líneas del diseño del tablero aquí...)
        ]

        # Diccionario con las posiciones iniciales de las fichas de cada jugador (en la cárcel)
        self.posiciones = {
            'A': {'a1': (14, 2), 'a2': (14, 4), 'a3': (12, 2), 'a4': (12, 4)},
            'B': {'b1': (2, 2), 'b2': (4, 2), 'b3': (2, 4), 'b4': (4, 4)},
            'C': {'c1': (2, 14), 'c2': (2, 12), 'c3': (4, 12), 'c4': (4, 14)},
            'D': {'d1': (14, 14), 'd2': (12, 14), 'd3': (14, 12), 'd4': (12, 12)}
        }

        # Diccionario con las posiciones de inicio de cada jugador
        self.punto_star = {
            'A': (12, 7),  # a_s
            'B': (7, 4),   # b_s
            'C': (4, 9),   # c_s
            'D': (9, 12)   # d_s
        }

    def dibujar_tablero(self):
        """Dibuja el tablero con las posiciones actualizadas de las fichas."""
        # Copia el diseño del tablero para evitar modificar el original
        tablero_actual = [fila.copy() for fila in self.diseno]

        # Coloca las fichas en sus posiciones actuales
        for jugador, fichas in self.posiciones.items():
            for ficha, (fila, col) in fichas.items():
                tablero_actual[fila][col] = f" {ficha} |"

        # Imprime el tablero en consola
        for fila in tablero_actual:
            print("".join(fila))

    def sacar_ficha_de_carcel(self, jugador, ficha):
        """Mueve una ficha del jugador desde la cárcel a su punto de inicio."""
        if ficha in self.posiciones[jugador]:
            fila_carcel, col_carcel = self.posiciones[jugador][ficha]
            self.diseno[fila_carcel][col_carcel] = "*****"  # Limpia la celda de la cárcel
            self.posiciones[jugador][ficha] = self.punto_star[jugador]  # Mueve la ficha al punto de inicio
            print(f"Ficha {ficha} del jugador {jugador} ha salido de la cárcel.")


def clear_console():
    """Limpia la consola antes de cada turno para mejorar la visualización."""
    os.system('cls' if os.name == 'nt' else 'clear')


def main():
    """Función principal que maneja el flujo del juego."""
    tablero = Tablero()  # Crea el tablero
    dados = Dados()  # Crea los dados

    jugadores = ['A', 'B', 'C', 'D']  # Lista de jugadores
    turno_actual = 0  # Índice del jugador actual en la lista

    while True:  # Bucle infinito para jugar turnos continuamente
        clear_console()  # Limpia la pantalla antes de cada turno
        print(f"\n--- Turno del Jugador {jugadores[turno_actual]} ---")
        tablero.dibujar_tablero()  # Dibuja el tablero con las fichas actuales

        # Lanzamiento de los dados
        dado1, dado2 = dados.lanzar_dados()
        print(f"Resultado del lanzamiento: {dado1}, {dado2}")

        # Verifica si los dados permiten sacar una ficha de la cárcel
        if dados.es_cinco(dado1, dado2):
            print("¡Puedes sacar una ficha de la cárcel!")
            try:
                # Solicita al usuario que ingrese qué ficha sacar
                ficha = input("¿Qué ficha deseas sacar de la cárcel? (ej. a1, b2, etc.): ").lower()

                # Verifica si la ficha ingresada pertenece al jugador actual
                if ficha in tablero.posiciones[jugadores[turno_actual]]:
                    tablero.sacar_ficha_de_carcel(jugadores[turno_actual], ficha)
                else:
                    print("Ficha no válida. Intenta de nuevo.")
            except ValueError:
                print("Entrada no válida. Intenta de nuevo.")
        else:
            print("No puedes sacar fichas de la cárcel. Turno pasado.")

        input("Presiona Enter para continuar...")  # Pausa antes del siguiente turno
        turno_actual = (turno_actual + 1) % len(jugadores)  # Cambia al siguiente jugador


# Ejecuta el juego si el script se ejecuta directamente
if __name__ == "__main__":
    main()
