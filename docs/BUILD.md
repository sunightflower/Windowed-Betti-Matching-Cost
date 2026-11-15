# Установка зависимостей и сборка Betti-Matching-3D

Первому ноутбуку достаточно зависимостей Python из `requirements.txt`. Второму дополнительно нужен скомпилированный модуль Betti-Matching-3D для того же интерпретатора, который используется ядром Jupyter.

Зафиксированная версия исходников: [db623be7456ae451beb422af3bbcea94e9863a8f](https://github.com/nstucki/Betti-Matching-3D/tree/db623be7456ae451beb422af3bbcea94e9863a8f).

## Windows

Нужны Git, Visual Studio 2022 или Build Tools с компонентами C++ и Windows SDK. Открыть Developer PowerShell и активировать нужное Python-окружение.

```powershell
$studyRoot = (Get-Location).Path
python -m pip install -r (Join-Path $studyRoot 'requirements.txt')
python -m pip install 'cmake>=3.20,<4' 'pybind11==3.0.1'
$bmRoot = Join-Path (Split-Path $studyRoot -Parent) 'Betti-Matching-3D'
git clone https://github.com/nstucki/Betti-Matching-3D.git $bmRoot
Set-Location $bmRoot
git checkout db623be7456ae451beb422af3bbcea94e9863a8f
git apply --ignore-whitespace (Join-Path $studyRoot 'patches/msvc_preprocessor_tokens.patch')
$bmPython = python -c 'import sys; print(sys.executable)'
$bmPybind = python -m pybind11 --cmakedir
cmake -S . -B build -G 'Visual Studio 17 2022' -A x64 -DPYBIND11_FINDPYTHON=ON "-DPython_EXECUTABLE=$bmPython" "-Dpybind11_DIR=$bmPybind"
cmake --build build --config Release --target betti_matching
```

Модуль `.pyd` появится в `Betti-Matching-3D/build/Release`. Указать абсолютный путь к этому каталогу в `BM_MODULE_DIR` первого кодового блока второго ноутбука. Сам файл `.pyd` в путь не включать.

Если каталог Betti-Matching-3D уже существует, использовать отдельный чистый каталог для этой сборки. Повторно применять тот же патч к уже исправленным исходникам не нужно.

## Linux

Нужен компилятор C++ и Git. Из корня репозитория исследования:

```sh
study_root="$PWD"
python -m pip install -r "$study_root/requirements.txt"
python -m pip install 'cmake>=3.20,<4' 'pybind11==3.0.1'
git clone https://github.com/nstucki/Betti-Matching-3D.git ../Betti-Matching-3D
cd ../Betti-Matching-3D
git checkout db623be7456ae451beb422af3bbcea94e9863a8f
git apply --ignore-whitespace "$study_root/patches/msvc_preprocessor_tokens.patch"
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DPYBIND11_FINDPYTHON=ON -DPython_EXECUTABLE="$(python -c 'import sys; print(sys.executable)')" -Dpybind11_DIR="$(python -m pybind11 --cmakedir)"
cmake --build build --target betti_matching -j2
```

Указать абсолютный путь к `Betti-Matching-3D/build` в `BM_MODULE_DIR` второго ноутбука.

## Запуск ноутбуков

Проверить выбранный интерпретатор командой `import sys; print(sys.executable)` внутри Jupyter. Бинарник должен соответствовать этому Python и операционной системе.

1. `01_reproduce_tables.ipynb`: выполнить все ячейки. Для сохранения таблиц и рисунков включить `SAVE_OUTPUTS=True`.
2. `02_measure_cost.ipynb`: сначала оставить `MODE='quick'` и выполнить все ячейки. Для записи результата включить `SAVE_RESULTS=True`; JSON сохраняется последней ячейкой после контроля функции и градиента.
3. Для полного режима загрузить архив по [INPUT_DATA.md](INPUT_DATA.md), выбрать `MODE='full'` и нужное значение `STAGE`.

Патч заменяет только альтернативные логические токены в директивах препроцессора. Linux-сборка и быстрый режим проверены на Python 3.12 и GCC 14.2.
