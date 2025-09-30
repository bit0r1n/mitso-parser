# 🍉 MITSO Parser
Парсер данных из сервисов МИТСО

## Установка
Установка производится запуском `nimble install https://github.com/bit0r1n/mitso-parser`

Для работы с библиотекой при компиляции необходимо добавить параметр `-d:ssl` для выполнения HTTPS запросов

## Пример использования

### Получение расписания группы (модули `schedule` и `wrapper`)

> [!WARNING]
> Модуль `schedule` содержит реализацию получения данных посредством парсинга пользовательского интерфейса сайта, что на момент написания текста уже не является рекомендуемым инструментом, рекомендуется использовать обертку над API из модуля `wrapper`

```nim
import asyncdispatch, sequtils, strutils
import mitso/[wrapper, helpers, typedefs]

proc main() {.async.} =
  let fetchedGroups = await getAllGroups() # Выполняет загрузку групп со всех факультетов -> форм обучения -> курсов
  # Может вылезти ошибка рейтлимита, так что стоит также отлавливать `ScheduleServiceError`

  echo "Введи номер группы"
  let
    input = readLine(stdin)
    groups = fetchedGroups.filter do (x: Group) -> bool: x.display.contains(input)

  echo if groups.len == 0: "Группы не нашлось =(" else: "Найдены группы: " & $groups

waitFor main()
```

### Получение баланса студента (модуль `account`)

```nim
import asyncdispatch
import mitso/[account, typedefs]

proc main() {.async.} =
  echo "Введи номер счета"
  let input = readLine(stdin)

  try:
    let account = await fetchAccount(input, input)

    echo account.fullName
    echo "Баланс: ", account.balance

  except AccountFailedLoginError:
    echo "Не удалось войти в аккаунт"

waitFor main()
```
