PRACTICAL 1


1(a) Depth First Search — DFS 


graph = {
    "a": ({"b": 1, "d": 2, "e": 3}, 4),
    "b": ({"c": 2, "d": 1}, 3),
    "c": ({"f": 3}, 2),
    "d": ({"f": 2}, 3),
    "e": ({"d": 3, "f": 4}, 2),
    "f": ({}, 0)
}


def a_star(graph, source, dest):

    open_list = {
        source: (0, graph[source][1])
    }

    path = {
        source: None
    }

    cost = {
        source: 0
    }

    while open_list:

        # Select node with minimum f(n) = g(n) + h(n)
        current = min(
            open_list,
            key=lambda x: open_list[x][0] + open_list[x][1]
        )

        del open_list[current]

        if current == dest:

            result = []

            while current is not None:
                result.append(current)
                current = path[current]

            result.reverse()

            return result, cost[dest]

        print("\nConnected nodes of", current)

        for neighbor, edge_cost in graph[current][0].items():

            new_cost = cost[current] + edge_cost

            if neighbor not in cost or new_cost < cost[neighbor]:

                cost[neighbor] = new_cost
                path[neighbor] = current

                open_list[neighbor] = (
                    new_cost,
                    graph[neighbor][1]
                )

                print(
                    neighbor,
                    "g(n) =", new_cost,
                    "h(n) =", graph[neighbor][1],
                    "f(n) =", new_cost + graph[neighbor][1]
                )

    return None, None


# Driver Code
source = input("Enter source vertex: ").lower()
dest = input("Enter destination vertex: ").lower()

path, total_cost = a_star(graph, source, dest)

if path:
    print("\nShortest Path:", " -> ".join(path))
    print("Total Cost:", total_cost)
else:
    print("Path not found.")
1(b) Breadth First Search — BFS
import collections

def bfs(graph, root):
    seen, queue = set([root]), collections.deque([root])

    while queue:
        vertex = queue.popleft()
        visit(vertex)

        for node in graph[vertex]:
            if node not in seen:
                seen.add(node)
                queue.append(node)


# all paths
def allpath(st, end, gr):
    todo = [(st, [st])]

    while len(todo):
        node, path = todo.pop(0)

        for next_node in gr[node]:

            if next_node in path:
                continue

            print("Ideal Solution")

            if next_node == end:
                yield path + [next_node]

            else:
                todo.append(
                    (next_node, path + [next_node])
                )


def visit(n):
    print(n)


def bfs_shortest_path(graph, source, destination):

    checked = []

    queue = [[source]]

    if source == destination:
        return "SOURCE IS DESTINATION."

    while queue:

        path = queue.pop(0)

        node = path[-1]

        if node not in checked:

            neighbours = graph[node]

            for neighbour in neighbours:

                new_path = list(path)

                new_path.append(neighbour)

                queue.append(new_path)

                if neighbour == destination:
                    return new_path

            checked.append(node)

    return "PATH DOES NOT EXIST."


graph = {
    'A': ['B','D'],
    'B': ['C','F'],
    'C': ['E','G'],
    'G': ['E'],
    'E': ['B','F'],
    'F': ['A'],
    'D': ['F']
}


print("GRAPH TRAVERSAL:")

bfs(graph, 'A')

print("\nAll paths is:")

[print(x) for x in allpath('A', 'E', graph)]

print(
    "SHORTEST PATH OF GRAPH IS :",
    bfs_shortest_path(graph, 'A', 'E')
)

output:

Your notebook shows the BFS practical together with graph traversal, all paths and shortest path, with the shortest path shown as:
['A', 'B', 'C', 'E']



Prac 2: 

2(a) N-Queen Problem

def print_board(board):
    for row in board:
        print(" ".join(row))
    print()


def check_q(board, row, col, n):
    # Check column
    for i in range(row):
        if board[i][col] == 'Q':
            return False

    # Check upper-left diagonal
    i, j = row, col
    while i >= 0 and j >= 0:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j -= 1

    # Check upper-right diagonal
    i, j = row, col
    while i >= 0 and j < n:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j += 1

    return True


def solve_queens(board, row, n):
    if row == n:
        print_board(board)
        return True

    for col in range(n):
        if check_q(board, row, col, n):
            board[row][col] = 'Q'

            if solve_queens(board, row + 1, n):
                return True

            board[row][col] = '.'

    return False


def queens():
    n = int(input("Enter value of N: "))
    board = []

    for i in range(n):
        row = []
        for j in range(n):
            row.append('.')
        board.append(row)

    if not solve_queens(board, 0, n):
        print("No Solution Found")


queens()

2(b) Tower of Hanoi

def tower_of_hanoi(n, source, auxiliary, destination):
    if n == 1:
        print(f"Move disk 1 from {source} to {destination}")
        return

    # Move n-1 disks from source to auxiliary
    tower_of_hanoi(n - 1, source, destination, auxiliary)

    # Move the largest disk from source to destination
    print(f"Move disk {n} from {source} to {destination}")

    # Move n-1 disks from auxiliary to destination
    tower_of_hanoi(n - 1, auxiliary, source, destination)


# Main program
n = int(input("Enter number of disks: "))

tower_of_hanoi(n, 'A', 'B', 'C')


Prac 3:


3(a) Alpha Beta Search 

tree = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F', 'G'],
    'D': [4, 3],
    'E': [6, 2],
    'F': [2, 1],
    'G': [9, 5]
}


def minimax_alpha_beta(node, depth, alpha, beta, max_player):

    # Base case: if we are at a leaf node, return the score.
    if depth == 0:
        if node in tree:
            # For this simple tree, the children are the scores.
            return tree[node][0] if max_player else tree[node][0]
        else:
            return node

    # Recursive case:

    # Maximizing player's turn (MAX)
    if max_player:
        value = float('-inf')

        for child in tree[node]:

            # Recursively call the function for the child node.
            value = max(
                value,
                minimax_alpha_beta(
                    child,
                    depth - 1,
                    alpha,
                    beta,
                    False
                )
            )

            alpha = max(alpha, value)

            if alpha >= beta:
                print(f"Pruning branch at node {node}")
                break

        return value

    # Minimizing player's turn (MIN)
    else:
        value = float('inf')

        for child in tree[node]:

            # Recursively call the function for the child node.
            value = min(
                value,
                minimax_alpha_beta(
                    child,
                    depth - 1,
                    alpha,
                    beta,
                    True
                )
            )

            beta = min(beta, value)

            if beta <= alpha:
                print(f"Pruning branch at node {node}")
                break

        return value


# Start the search from the root node 'A'
best_score = minimax_alpha_beta(
    'A',
    2,
    float('-inf'),
    float('inf'),
    True
)

print(f"The best score is: {best_score}")

3(b) Hill Climbing

import random

distance = [
    [0, 2, 9, 10],
    [2, 0, 6, 4],
    [9, 6, 0, 3],
    [10, 4, 3, 0]
]


def get_cost(tour):
    cost = 0

    for i in range(len(tour)):
        cost += distance[tour[i - 1]][tour[i]]
        print("Cost of tour:", cost)

    return cost


def get_neighbour(tour):
    a, b = random.sample(range(len(tour)), 2)
    tour[a], tour[b] = tour[b], tour[a]
    return tour


def hill_climb():

    current = [0, 1, 2, 3]
    random.shuffle(current)

    current_cost = get_cost(current)

    print("Starting tour:", current, "Cost:", current_cost)

    for i in range(10):

        neighbor = current[:]

        print("Current neighbor:", neighbor)

        neighbor = get_neighbour(neighbor)

        print("Connected neighbor:", neighbor)

        # Calculate the cost of the neighbour
        neighbour_cost = get_cost(neighbor)

        if neighbour_cost < current_cost:

            current = neighbor

            print("Current neighbor:", current)

            current_cost = neighbour_cost

            print("Current Cost:", current_cost)

            print(
                "Better tour found:",
                current,
                "Cost:",
                current_cost
            )

    return current, current_cost 


best_tour, best_cost = hill_climb()

print("\nBest tour:", best_tour)
print("Best cost:", best_cost)


Prac 4:


A* Algorithm:  
graph = {
    "a": ({"b": 1, "d": 2, "e": 3}, 4),
    "b": ({"c": 2, "d": 1}, 3),
    "c": ({"f": 3}, 2),
    "d": ({"f": 2}, 3),
    "e": ({"d": 3, "f": 4}, 2),
    "f": ({}, 0)
}


def a_star(graph, source, dest):

    open_list = {
        source: (0, graph[source][1])
    }

    path = {
        source: None
    }

    cost = {
        source: 0
    }

    while open_list:

        # Select node with minimum f(n) = g(n) + h(n)
        current = min(
            open_list,
            key=lambda x: open_list[x][0] + open_list[x][1]
        )

        del open_list[current]

        if current == dest:

            result = []

            while current is not None:
                result.append(current)
                current = path[current]

            result.reverse()

            return result, cost[dest]

        print("\nConnected nodes of", current)

        for neighbor, edge_cost in graph[current][0].items():

            new_cost = cost[current] + edge_cost

            if neighbor not in cost or new_cost < cost[neighbor]:

                cost[neighbor] = new_cost
                path[neighbor] = current

                open_list[neighbor] = (
                    new_cost,
                    graph[neighbor][1]
                )

                print(
                    neighbor,
                    "g(n) =", new_cost,
                    "h(n) =", graph[neighbor][1],
                    "f(n) =", new_cost + graph[neighbor][1]
                )

    return None, None


# Driver Code
source = input("Enter source vertex: ").lower()
dest = input("Enter destination vertex: ").lower()

path, total_cost = a_star(graph, source, dest)

if path:
    print("\nShortest Path:", " -> ".join(path))
    print("Total Cost:", total_cost)
else:
    print("Path not found.")


##############################################     4(b) Greedy Best First Search:

graph = {
    "a": ({"b": 1, "d": 2, "e": 3}, 4),
    "b": ({"c": 2, "d": 1}, 3),
    "c": ({"f": 3}, 2),
    "d": ({"f": 2}, 3),
    "e": ({"d": 3, "f": 4}, 2),
    "f": ({}, 0)
}


def greedy_search_rec(graph, prev, dst, path, q):

    print(
        "\nConnected nodes of current node",
        prev,
        "with h(n) values:"
    )

    for n in graph[prev][0]:

        if n not in path:
            q[n] = graph[n][1]
            print(n, "-> h(n) =", q[n])

    while q:

        mn = min(q, key=q.get)

        print("Taking minimum h(n) vertex:", mn)

        del q[mn]

        if dst == mn:
            return path + [dst]

        new_path = greedy_search_rec(
            graph,
            mn,
            dst,
            path + [mn],
            q
        )

        if new_path:
            return new_path

    return []


# Driver Code
source = input("Enter source vertex: ").lower()
dest = input("Enter destination vertex: ").lower()

path = greedy_search_rec(
    graph,
    source,
    dest,
    [source],
    {}
)

if path:
    print("\nGreedy Best First Search Path:")
    print(" -> ".join(path))
else:
    print("\nPath not found.")
    print("Path not found!")

prac 5:


5(a) Water Jug Problem:

from collections import deque


def is_visited(state, visited):
    return state in visited


def water_jug_bfs():

    max_a, max_b = 6, 5

    visited = set()

    queue = deque()

    queue.append((0, 0))

    while queue:

        a, b = queue.popleft()

        if is_visited((a, b), visited):
            continue

        visited.add((a, b))

        print(f"Jug A: {a}L, Jug B: {b}L")

        # Goal: get exactly 2L in either jug
        if a == 2 or b == 2:
            print("Found a Solution!")
            return

        # All possible operations
        possible_states = [

            # Fill A
            (max_a, b),

            # Fill B
            (a, max_b),

            # Empty A
            (0, b),

            # Empty B
            (a, 0),

            # Pour A -> B
            (
                a - min(a, max_b - b),
                b + min(a, max_b - b)
            ),

            # Pour B -> A
            (
                a + min(b, max_a - a),
                b - min(b, max_a - a)
            )
        ]

        for state in possible_states:

            if state not in visited:
                queue.append(state)

    print("No solution found!")


water_jug_bfs()

5.b Travelling Salesman Problem

from itertools import permutations


dist = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]


n = len(dist)

cities = range(1, n)

min_distance = float('inf')

best_path = None


for path in permutations(cities):

    current_path = (0,) + path + (0,)

    distance = 0

    for i in range(len(current_path) - 1):

        distance += dist[
            current_path[i]
        ][
            current_path[i + 1]
        ]

    if distance < min_distance:

        min_distance = distance

        best_path = current_path


print("Shortest distance:", min_distance)

print("Best path:", best_path)


prac 6:

6(a) Missionaries and Cannibals:

from collections import deque


moves = [
    (2, 0),
    (0, 2),
    (1, 1),
    (1, 0),
    (0, 1)
]


def is_valid(m_left, c_left, m_right, c_right):

    if (
        m_left < 0
        or c_left < 0
        or m_right < 0
        or c_right < 0
    ):
        return False

    if (
        (m_left > 0 and m_left < c_left)
        or
        (m_right > 0 and m_right < c_right)
    ):
        return False

    return True


def solve():

    start = (3, 3, 1)

    goal = (0, 0, 0)

    queue = deque()

    queue.append((start, [start]))

    visited = set()

    while queue:

        (m_left, c_left, boat), path = queue.popleft()

        if (m_left, c_left, boat) in visited:
            continue

        visited.add((m_left, c_left, boat))

        if (m_left, c_left, boat) == goal:
            return path

        for m, c in moves:

            if boat == 1:

                new_m_left = m_left - m
                new_c_left = c_left - c
                new_boat = 0

            else:

                new_m_left = m_left + m
                new_c_left = c_left + c
                new_boat = 1

            new_m_right = 3 - new_m_left
            new_c_right = 3 - new_c_left

            if is_valid(
                new_m_left,
                new_c_left,
                new_m_right,
                new_c_right
            ):

                new_state = (
                    new_m_left,
                    new_c_left,
                    new_boat
                )

                if new_state not in visited:

                    queue.append(
                        (
                            new_state,
                            path + [new_state]
                        )
                    )

    return None


steps = solve()

if steps:

    for i, (m, c, b) in enumerate(steps):

        side = "Left" if b == 1 else "Right"

        print(
            f"Step {i}: Missionaries Left: {m}, "
            f"Cannibals Left: {c}, Boat on: {side}"
        )

else:

    print("No Solution found.")

6(b) Number Puzzle:

from collections import deque


goal = '123456780'


moves = {
    0: [1, 3],
    1: [0, 2, 4],
    2: [1, 5],
    3: [0, 4, 6],
    4: [1, 3, 5, 7],
    5: [2, 4, 8],
    6: [3, 7],
    7: [4, 6, 8],
    8: [5, 7]
}


def bfs(start):

    visited = set()

    queue = deque([(start, [])])

    while queue:

        state, path = queue.popleft()

        if state == goal:
            return path + [state]

        if state in visited:
            continue

        visited.add(state)

        zero = state.index('0')

        for move in moves[zero]:

            new_state = list(state)

            new_state[zero], new_state[move] = (
                new_state[move],
                new_state[zero]
            )

            queue.append(
                (
                    ''.join(new_state),
                    path + [state]
                )
            )

    return None


start = '123456708'

solution = bfs(start)

if solution:

    print("Steps to solve:")

    for s in solution:

        print(s[0:3])
        print(s[3:6])
        print(s[6:9])
        print("---")

else:

    print("No solution found.")


Prac 7:

7(a) Shuffle Deck of Cards:

import random


# Step 1: Create the deck
suits = [
    'Hearts',
    'Diamonds',
    'Clubs',
    'Spades'
]

ranks = [
    '2',
    '3',
    '4',
    '5',
    '6',
    '7',
    '8',
    '9',
    '10',
    'Jack',
    'Queen',
    'King',
    'Ace'
]


# Combine suits and ranks
deck = [
    rank + " of " + suit
    for suit in suits
    for rank in ranks
]


# Step 2: Shuffle the deck
random.shuffle(deck)


# Step 3: Display the shuffled deck
print("Shuffled Deck of Cards:")

for card in deck:
    print(card)

7(b) Tic-Tac-Toe Game:

board = [' ' for _ in range(9)]

player = 'X'


def show_board():

    print(f' | {board[0]} | {board[1]} | {board[2]} |')
    print('-------------')

    print(f' | {board[3]} | {board[4]} | {board[5]} |')
    print('-------------')

    print(f' | {board[6]} | {board[7]} | {board[8]} |')


def is_winner(p):

    return (
        (board[0] == p and board[1] == p and board[2] == p)
        or
        (board[3] == p and board[4] == p and board[5] == p)
        or
        (board[6] == p and board[7] == p and board[8] == p)
        or
        (board[0] == p and board[3] == p and board[6] == p)
        or
        (board[1] == p and board[4] == p and board[7] == p)
        or
        (board[2] == p and board[5] == p and board[8] == p)
        or
        (board[0] == p and board[4] == p and board[8] == p)
        or
        (board[2] == p and board[4] == p and board[6] == p)
    )


def is_tie():

    # Checks if the board is full.
    return ' ' not in board


def game():

    global player

    while True:

        show_board()

        try:

            move = int(
                input(
                    f"Player {player}, "
                    "enter position (0-8): "
                )
            )

            if 0 <= move <= 8 and board[move] == ' ':

                board[move] = player

                if is_winner(player):

                    show_board()

                    print(
                        f"Player {player} wins!"
                    )

                    break

                elif is_tie():

                    show_board()

                    print("It's a tie!")

                    break

                # Switch player
                if player == 'X':
                    player = 'O'
                else:
                    player = 'X'

            else:

                print("Invalid move. Try again.")

        except (ValueError, IndexError):

            print(
                "Invalid input. "
                "Enter a number between 0 and 8."
            )


game()


Prac 8:

Practical 8(a) — Block of Word Problem:

ini_state = ['1', '2', '3', '5', '6', '0', '8', '9', '7']
go_state  = ['1', '2', '3', '7', '6', '0', '9', '8', '5']

# Initialize lists with the same length or copy them directly
num1 = list(ini_state)
num2 = list(go_state)
num3 = [''] * len(ini_state)

for i in range(len(ini_state)):
    if num1[i] == num2[i]:
        continue
    else:
        num3[i] = num1[i]
        num1[i] = num2[i]
        num2[i] = num3[i]

# Example output to check results
print("Modified num1:", num1)
print("Modified num2:", num2)




8(b) Constraint Satisfaction Problem:

import itertools


variables = ["A", "B", "C", "D"]

colors = [
    "Red",
    "Blue",
    "Yellow",
    "pink"
]


all_assignments = itertools.product(
    colors,
    repeat=len(variables)
)


def valid(i):

    A, B, C, D = i

    return (
        (A != B)
        and
        (B != C)
        and
        (C != D)
        and
        (A != D)
    )


solutions = []

for i in all_assignments:

    if valid(i):

        solutions.append(
            dict(zip(variables, i))
        )


print("Valid colorings of the map:")

for sol in solutions:

    print(sol)

Prac 9:

9(a) Associative Law:

# Associative Law Verification using if-else

print("ASSOCIATIVE LAW VERIFICATION")


# Boolean Inputs
print("\nEnter Boolean Values (0 = False, 1 = True)")

A = bool(int(input("Enter A: ")))
B = bool(int(input("Enter B: ")))
C = bool(int(input("Enter C: ")))


# Boolean AND
lhs = (A and B) and C
rhs = A and (B and C)


print("\n----- Boolean AND -----")

print(
    "LHS = (A AND B) AND C =",
    lhs
)

print(
    "RHS = A AND (B AND C) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Associative Law is Verified.")

else:

    print("Result: LHS ≠ RHS")
    print("Associative Law is Not Verified.")


# Boolean OR
lhs = (A or B) or C
rhs = A or (B or C)


print("\n----- Boolean OR -----")

print(
    "LHS = (A OR B) OR C =",
    lhs
)

print(
    "RHS = A OR (B OR C) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Associative Law is Verified.")

else:

    print("Result: LHS ≠ RHS")
    print("Associative Law is Not Verified.")


# Arithmetic Inputs
print("\nEnter Three Numbers")

a = int(input("Enter a: "))
b = int(input("Enter b: "))
c = int(input("Enter c: "))


# Arithmetic Addition
lhs = (a + b) + c
rhs = a + (b + c)


print("\n----- Arithmetic Addition -----")

print(
    "LHS = (a + b) + c =",
    lhs
)

print(
    "RHS = a + (b + c) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Associative Law is Verified.")

else:

    print("Result: LHS ≠ RHS")
    print("Associative Law is Not Verified.")


# Arithmetic Multiplication
lhs = (a * b) * c
rhs = a * (b * c)


print("\n----- Arithmetic Multiplication -----")

print(
    "LHS = (a * b) * c =",
    lhs
)

print(
    "RHS = a * (b * c) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Associative Law is Verified.")

else:

    print("Result: LHS ≠ RHS")
    print("Associative Law is Not Verified.")

9(b) Distributive Law:

# Program to Verify Distributive Law

print("=======================================")
print("      DISTRIBUTIVE LAW VERIFICATION")
print("=======================================")


# ---------- Boolean Inputs ----------
print("\nEnter Boolean Values (0 = False, 1 = True)")

A = bool(int(input("Enter A: ")))
B = bool(int(input("Enter B: ")))
C = bool(int(input("Enter C: ")))


# ---------- Boolean AND over OR ----------
print("\n----- Boolean AND over OR -----")

lhs = A and (B or C)
rhs = (A and B) or (A and C)


print(
    "LHS = A AND (B OR C) =",
    lhs
)

print(
    "RHS = (A AND B) OR (A AND C) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Distributive Law Verified")

else:

    print("Result: LHS ≠ RHS")
    print("Distributive Law Not Verified")


# ---------- Boolean OR over AND ----------
print("\n----- Boolean OR over AND -----")

lhs = A or (B and C)
rhs = (A or B) and (A or C)


print(
    "LHS = A OR (B AND C) =",
    lhs
)

print(
    "RHS = (A OR B) AND (A OR C) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Distributive Law Verified")

else:

    print("Result: LHS ≠ RHS")
    print("Distributive Law Not Verified")


# ---------- Arithmetic Inputs ----------
print("\nEnter Arithmetic Values")

a = int(input("Enter a: "))
b = int(input("Enter b: "))
c = int(input("Enter c: "))


# ---------- Arithmetic Distributive Law ----------
print("\n----- Arithmetic Distributive Law -----")

lhs = a * (b + c)
rhs = (a * b) + (a * c)


print(
    "LHS = a * (b + c) =",
    lhs
)

print(
    "RHS = (a * b) + (a * c) =",
    rhs
)


if lhs == rhs:

    print("Result: LHS = RHS")
    print("Distributive Law Verified")

else:

    print("Result: LHS ≠ RHS")
    print("Distributive Law Not Verified")


print("\nProgram Completed Successfully.")


Prac 10:


10(a) Predicate Examples
1. Batman → Cricketer → Sportsman → Famous Person:

batman(sachin).
batman(virat).
batman(rahul).
batman(dhoni).

cricketer(X) :- batman(X).

sportsman(X) :- cricketer(X).

famous(X) :- sportsman(X).

2. Teacher → Employee → Human → Living Being:

teacher(Anita).
teacher(Raj).
teacher(Meera).
teacher(Rahul).

employee(X) :- teacher(X).

human(X) :- employee(X).

livingbeing(X) :- human(X).

3. Student → Learner → Knowledge Seeker → Future Professional:

student(Riya).
student(Amit).
student(Sam).
student(Neha).

learner(X) :- student(X).

knowledgeseeker(X) :- learner(X).

futureprofessional(X) :- knowledgeseeker(X).

4. Dog → Animal → Pet → Living Being
dog(Tommy).
dog(Bruno).
dog(Lucky).
dog(Rocky).

animal(X) :- dog(X).

pet(X) :- animal(X).

livingbeing(X) :- pet(X).

5. Book → Knowledge Source → Educational Material → Valuable Resource
book(Physics).
book(Maths).
book(History).
book(Computers).

knowledgesource(X) :- book(X).

educationalmaterial(X) :- knowledgesource(X).

valuableresource(X) :- educationalmaterial(X).


10(b) Family Tree / Given Predicates:

Family Facts:

male(john).
male(mike).
male(david).

female(lisa).
female(susan).
female(anna).

parent(john, mike).
parent(john, lisa).

parent(susan, mike).
parent(susan, lisa).

parent(mike, david).
parent(anna, david).

Rules::::::

father(F, C) :-
    male(F),
    parent(F, C).

mother(M, C) :-
    female(M),
    parent(M, C).

grandfather(GF, C) :-
    male(GF),
    parent(GF, P),
    parent(P, C).

grandmother(GM, C) :-
    female(GM),
    parent(GM, P),
    parent(P, C).

sibling(X, Y) :-
    parent(P, X),
    parent(P, Y),
    X \= Y.





