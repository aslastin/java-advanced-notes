# IO

`Char` - 16 битный тип беззнаковый - но размер не гарантируется, гарантируется сколько максимум вмещается.

`read` гарантирует, что не вернет 0, будет блокирующие ожидание.

`write` принимает аргументом `int` - было для согласования с `read` (хотя это плохой вариант, так как `read` может вернуть `-1`).

Перед закрытиям все данные протолкнутся (вызовется `flush`).

`Character.toUpperCase` - если бука не строчная - вернет то, что дали на вход.

Размер буфера по умолчанию также платформозависимый.

В java используется big-endian для `DataInput` / `DataOutput`.

`RandomAccessFile`: при синхронном чтении / записи возможна работа с файлом одновременно из двух мест.

- У дескриптора под капотом строка
- У корневой директории `getParent()` вернет `null`
- mkdir/mkdkirs возвращают флажок (без исключений)
- list() в случае неудачи возвращает `null`

- normalize(a/b/../c/./d) -> a/c/d
- A/c/d.resolve(hello) -> a/c/d/hello
- `Files.exists/notExsits` - файл внутри каталога, к которому нет доступа
- В `java.nio` кодировка всегда по умолачанию utf-8
