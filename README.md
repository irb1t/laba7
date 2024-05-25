# laba7
import tkinter as tk
from tkinter import scrolledtext
import itertools
import time

# Алгоритмическая реализация (рекурсивная)
def place_books_recursive(K, T):
    def helper(books, bundles):
        if not books:
            yield bundles
        else:
            for i in range(len(bundles)):
                new_bundles = [list(bundle) for bundle in bundles]
                new_bundles[i].append(books[0])
                yield from helper(books[1:], new_bundles)

    return list(helper(list(range(1, K + 1)), [[] for _ in range(T)]))

# Реализация с использованием функций Python
def place_books_python(K, T):
    books = range(1, K + 1)
    all_combinations = itertools.product(range(T), repeat=K)
    results = []
    for combination in all_combinations:
        bundles = [[] for _ in range(T)]
        for book, bundle_index in zip(books, combination):
            bundles[bundle_index].append(book)
        results.append(bundles)
    return results

# Усложненная версия с ограничениями
def place_books_with_constraint(K, T, max_books_per_bundle):
    books = range(1, K + 1)
    all_combinations = itertools.product(range(T), repeat=K)
    valid_results = []
    
    for combination in all_combinations:
        bundles = [[] for _ in range(T)]
        for book, bundle_index in zip(books, combination):
            bundles[bundle_index].append(book)
        
        if all(len(bundle) <= max_books_per_bundle for bundle in bundles):
            valid_results.append(bundles)
    
    return valid_results

def evaluate_bundles(bundles):
    bundle_sizes = [len(bundle) for bundle in bundles]
    return max(bundle_sizes) - min(bundle_sizes)

def find_optimal_placement(K, T, max_books_per_bundle):
    valid_results = place_books_with_constraint(K, T, max_books_per_bundle)
    optimal_placement = min(valid_results, key=evaluate_bundles)
    return optimal_placement

# Графический интерфейс
def calculate_placement():
    K = int(entry_K.get())
    T = int(entry_T.get())
    max_books_per_bundle = int(entry_max_books.get())
    
    start_time = time.time()
    optimal_result = find_optimal_placement(K, T, max_books_per_bundle)
    end_time = time.time()
    
    result_str = f"Optimal Placement (time: {end_time - start_time:.6f} seconds):\n"
    for i, bundle in enumerate(optimal_result):
        result_str += f"Banderole {i + 1}: {bundle}\n"
    
    text_output.config(state=tk.NORMAL)
    text_output.delete(1.0, tk.END)
    text_output.insert(tk.INSERT, result_str)
    text_output.config(state=tk.DISABLED)

# Создание главного окна
root = tk.Tk()
root.title("Book Placement Optimization")

# Поле ввода для K
label_K = tk.Label(root, text="Number of books (K):")
label_K.grid(column=0, row=0, padx=10, pady=5, sticky=tk.W)
entry_K = tk.Entry(root, width=10)
entry_K.grid(column=1, row=0, padx=10, pady=5)

# Поле ввода для T
label_T = tk.Label(root, text="Number of banderoles (T):")
label_T.grid(column=0, row=1, padx=10, pady=5, sticky=tk.W)
entry_T = tk.Entry(root, width=10)
entry_T.grid(column=1, row=1, padx=10, pady=5)

# Поле ввода для максимального количества книг в одной бандероли
label_max_books = tk.Label(root, text="Max books per banderole:")
label_max_books.grid(column=0, row=2, padx=10, pady=5, sticky=tk.W)
entry_max_books = tk.Entry(root, width=10)
entry_max_books.grid(column=1, row=2, padx=10, pady=5)

# Кнопка запуска вычисления
button_calculate = tk.Button(root, text="Calculate", command=calculate_placement)
button_calculate.grid(column=0, row=3, columnspan=2, pady=10)

# Поле вывода результатов с прокруткой
text_output = scrolledtext.ScrolledText(root, width=50, height=20, wrap=tk.WORD, state=tk.DISABLED)
text_output.grid(column=0, row=4, columnspan=2, padx=10, pady=10)

# Запуск главного цикла приложения
root.mainloop()
