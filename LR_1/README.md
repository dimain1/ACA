# Тема 01 Введение в алгоритмы. Сложность. Поиск.

Цель работы: Освоить понятие вычислительной сложности алгоритма. Получить практические навыки
 реализации и анализа линейного и бинарного поиска. Научиться экспериментально подтверждать
 теоретические оценки сложности O(n) и O(log n)

Задание: 
 1. Реализовать функцию линейного поиска элемента в массиве.
 2. Реализовать функцию бинарного поиска элемента в отсортированном массиве.
 3. Провести теоретический анализ сложности обоих алгоритмов.
 4. Экспериментально сравнить время выполнения алгоритмов на массивах разного размера.
 5. Визуализировать результаты, подтвердив асимптотику O(n) и O(log n).


```PYTHON
# Программирование на языке высокого уровня (Python).
# Задание № 01. Вариант 11
# Выполнил: Черников Дмитрий Дмитриевич
# Группа: ПИЖ-б-о-23-2(1)
# E-mail: dima.chernikov.053@mail.ru

# search_comparison.py


# Импорт необходимых библиотек
import matplotlib.pyplot as plt
import timeit


def linear_search(arr, target):
    """
    Линейный поиск элемента в массиве.
    Возвращает индекс target или -1, если не найден.
    Сложность: O(n), где n - длина массива.
    """
    for i in range(len(arr)):      # O(n) - проход по всем элементам
        if arr[i] == target:       # O(1) - сравнение
            return i               # O(1) - возврат индекса
    return -1                      # O(1) - если не найден
    # Общая сложность: O(n)


def binary_search(arr, target):
    """
    Бинарный поиск элемента в отсортированном массиве.
    Возвращает индекс target или -1, если не найден.
    Сложность: O(log n), где n - длина массива.
    """
    left = 0                       # O(1) - инициализация
    right = len(arr) - 1           # O(1) - инициализация
    while left <= right:           # O(log n) - деление диапазона
        mid = (left + right) // 2  # O(1) - вычисление середины
        if arr[mid] == target:     # O(1) - сравнение
            return mid             # O(1) - возврат индекса
        elif arr[mid] < target:    # O(1) - сравнение
            left = mid + 1         # O(1) - сдвиг границы
        else:
            right = mid - 1        # O(1) - сдвиг границы
    return -1                      # O(1) - если не найден
    # Общая сложность: O(log n)


sizes = [1000, 2000, 5000, 10000, 50000, 100000, 500000, 1000000]


def generate_test_data(sizes):
    """
    Генерирует отсортированные массивы заданных размеров и целевые элементы.
    Возвращает словарь: {size: {'array': [...], 'targets': {...}}}
    Сложность: O(k*n), где k - количество размеров, n - размер массива.
    """
    data = {}
    for size in sizes:                # O(k)
        arr = list(range(size))       # O(n)
        targets = {
            'first': arr[0],          # O(1)
            'last': arr[-1],          # O(1)
            'absent': -1              # O(1)
        }
        data[size] = {'array': arr, 'targets': targets}  # O(1)
    return data                       # O(1)
# Общая сложность: O(k*n)


test_data = generate_test_data(sizes)


def measure_time(search_func, arr, target, repeat=10):
    times = []
    for _ in range(repeat):
        t = timeit.timeit(lambda: search_func(arr, target), number=1)
        times.append(t * 1000)
    return sum(times) / len(times)


results = {
    'linear_search': {},
    'binary_search': {}
}
for size, info in test_data.items():
    arr = info['array']
    targets = info['targets']
    results['linear_search'][size] = {}
    results['binary_search'][size] = {}
    for key, target in targets.items():
        results['linear_search'][size][key] = measure_time(
            linear_search, arr, target)
        results['binary_search'][size][key] = measure_time(
            binary_search, arr, target)


def plot_results(results, sizes):
    plt.figure(figsize=(12, 6))
    for alg in ['linear_search', 'binary_search']:
        y = [results[alg][size]['last'] for size in sizes]
        plt.plot(sizes, y, marker='o', label=alg)
    plt.xlabel('Размер массива')
    plt.ylabel('Время (мс)')
    plt.title('Время поиска (последний элемент)')
    plt.legend()
    plt.grid(True)
    plt.show()

    plt.figure(figsize=(12, 6))
    for alg in ['linear_search', 'binary_search']:
        y = [results[alg][size]['last'] for size in sizes]
        plt.plot(sizes, y, marker='o', label=alg)
    plt.xlabel('Размер массива')
    plt.ylabel('Время (мс, log scale)')
    plt.yscale('log')
    plt.title('Время поиска (логарифмическая шкала, последний элемент)')
    plt.legend()
    plt.grid(True)
    plt.show()

    # График в log-log масштабе
    plt.figure(figsize=(12, 6))
    for alg in ['linear_search', 'binary_search']:
        y = [results[alg][size]['last'] for size in sizes]
        plt.plot(sizes, y, marker='o', label=alg)
    plt.xlabel('Размер массива (log scale)')
    plt.ylabel('Время (мс, log scale)')
    plt.xscale('log')
    plt.yscale('log')
    plt.title('Время поиска (log-log scale, последний элемент)')
    plt.legend()
    plt.grid(True)
    plt.show()


plot_results(results, sizes)

```

<image src="time_complexity_plot.png">
<image src="time_complexity_plot_log.png">
<div>Линейный поиск (linear_search): теоретически O(n), время растет линейно с размером массива.
      Практически: время поиска первого элемента минимально, последнего/отсутствующего — максимально, график близок к прямой.
      Для последнего элемента требуется n сравнений.
<br>Бинарный поиск (binary_search): теоретически O(log n), время растет медленно, логарифмически.
      Практически: время почти не зависит от позиции элемента, график близок к логарифмической кривой.
      Для последнего элемента требуется log(n) сравнений.</div>
