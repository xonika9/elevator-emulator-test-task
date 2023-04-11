# Elevator Emulator Test Task

Тестовое задание на позицию Frontend-разработчика в компанию TrueConf.

Требуется разработать одностраничное приложение (SPA), эмулирующее работу лифтовой системы.


---

## Stack

- HTML
- CSS
- JavaScript
- Vue
- Vite

Link to [Vercel Deploy](https://elevator-emulator-test-task.vercel.app/)

## Реализация

Приложение состоит из схемы нескольких этажей с нумерацией и кнопками вызова, а также из самой шахты лифта с кабиной.

Количество этажей по умолчанию: 5

Количество шахт лифта по умолчанию: 1

По умолчанию лифт находится на 1 этаже в состоянии покоя (свободен). При нажатии на кнопку вызова лифт обрабатывает этот вызов в соответствии со следующими сценариями:

1. Если вызов осуществляется с этажа, на котором лифта нет – свободный лифт начинает движение к выбранному этажу со скоростью 1 этаж в секунду. Достигнув нужного этажа лифт 3 секунды «отдыхает» - индикация этого состояния реализована с помощью мигания.
После этого лифт снова переходит в состояние покоя и готов обработать следующий вызов.

2. Если в момент движения лифта осуществить вызов на другой этаж – этот вызов добавляется в очередь вызовов. Вызовы обрабатываются последовательно.

3. Вызов пропускается в случаях, если:

- лифт уже находится на выбранном этаже в состоянии покоя
- лифт уже находится в процессе обработки такого вызова (находится в
  движении к выбранному этажу)
- в очереди вызовов уже есть выбранный этаж

Также реализована дополнительная обвязка:

1. Табло индикации на кабине лифта, на котором в процессе движения отображается направление движения и целевой этаж.
2. Индикация кнопок вызова на этажах в случае, если:

- лифт уже находится в процессе обработки такого вызова (находится в
  движении к выбранному этажу)
- в очереди вызовов уже есть выбранный этаж

3. Состояние приложения (позиция лифта, очередь вызовов и т.д.) сохраняется при перезагрузке страницы.

Приложение спроектировано таким образом, чтобы его было легко масштабировать. В конфиге можно изменить количество этажей и лифтов.

При наличии двух и более свободных лифтов вызов обрабатывает лифт, наиболее близко расположенный к целевому этажу.
