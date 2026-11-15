# Загрузка входных массивов

Архив [H1_preds.npz](https://github.com/sunightflower/Windowed-Betti-Matching-Cost/releases/download/predictions/H1_preds.npz) доступен в [релизе predictions](https://github.com/sunightflower/Windowed-Betti-Matching-Cost/releases/tag/predictions). Обычное клонирование репозитория не загружает вложения релиза.

Сохранить архив под именем `predictions.npz` в рабочий каталог второго ноутбука либо указать абсолютный путь к файлу в `INPUT_PATH`.

## PowerShell

```powershell
Invoke-WebRequest -Uri 'https://github.com/sunightflower/Windowed-Betti-Matching-Cost/releases/download/predictions/H1_preds.npz' -OutFile predictions.npz
(Get-FileHash predictions.npz -Algorithm SHA256).Hash.ToLower()
```

## Linux

```sh
curl --fail --location 'https://github.com/sunightflower/Windowed-Betti-Matching-Cost/releases/download/predictions/H1_preds.npz' --output predictions.npz
sha256sum predictions.npz
```

Размер файла: 38 568 701 байт. SHA-256:

```text
0a200863ff8b01757132ae390443c51a0406dd3fcc6ce7e4e7003355b950af62
```

Переименование файла не меняет его контрольную сумму. По умолчанию второй ноутбук проверяет её до начала полного прогона.

## Формат

Архив NumPy открывается с `allow_pickle=False`. Каждый массив имеет форму `(125,256,128)` в порядке осей z,y,x.

| Ключи | Содержимое | Тип |
|---|---|---|
| `val0_y`, `test0_y` | Бинарная разметка двух половин X2 | bool |
| `val0_it500`, `val0_it1500`, `val0_it4000` | Вероятностные карты первой половины | float16 |
| `test0_it500`, `test0_it1500`, `test0_it4000` | Вероятностные карты второй половины | float16 |

Числа 500, 1500 и 4000 обозначают итерации обучения, после которых сохранялись состояния сети. В полном режиме `STAGE='calibration'` используется карта `val0_it4000`; при `STAGE='test'` используются три карты test.

## Происхождение

Исходные изображения ядер Mouse-Skull-Nuclei-CBG и разметка получены через [EmbedSeg](https://github.com/juglab/EmbedSeg); создатели набора — Jacqueline Tabler и Diana Afonso. Интенсивности усреднены по блокам 2 × 2 в плоскости xy, метки прорежены по тем же осям. Для бинарной разметки соприкасающиеся экземпляры разделены границей. X2 разделён по оси x на val и test. Вероятностные карты получены сетью 3D U-Net, обученной на отдельном изображении X1.

Описание проекта EmbedSeg: Lalit M., Tomancak P., Jug F. *Embedding-based Instance Segmentation in Microscopy*. MIDL 2021, PMLR 143, 399–415. [Первичный источник](https://proceedings.mlr.press/v143/lalit21a.html).
