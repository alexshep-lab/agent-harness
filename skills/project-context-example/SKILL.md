---
name: project-context-example
description: "SANITIZED EXAMPLE of a private project-context skill. Real version: context of an R&D project for a national energy operator. Use this skill ALWAYS when the user mentions: <project codename>, <client name>, <system name>, <domain terms: cascade, water balance, short-term planning>, <stakeholder surnames>, meeting preparation for this project, analysis of the project's ToR, or working with meeting transcripts. Trigger even on casual mentions of 'the meeting', 'the R&D', 'the ToR' — a skill that doesn't fire is worse than no skill."
---

> **Note for readers of this repo.** My real skills of this type stay private —
> they contain stakeholder names, negotiation history and client politics.
> This file preserves the exact *structure* with placeholders, because the
> structure is the transferable part.

# <R&D Project> — Контекст проекта

## Кто пользователь

<Имя>, аспирант + исполнитель в проекте. Работает под руководством <научрук>.

**Терминологический guardrail:** термин `<X>` НЕ должен упоминаться в
документах этого проекта — он зарезервирован для диссертации. Вместо него
всегда: «адаптивные алгоритмы», «современные численные методы», «методы
машинного обучения». (Агент, нарушивший это правило в черновике официального
документа, создаёт реальный ущерб — правило абсолютное.)

## Суть проекта

<2–3 абзаца: что делаем, для кого, масштаб (бюджет, сроки, этапы), текущий
статус. Числа обязательны — агент без чисел галлюцинирует масштаб.>

## Исполнители и стейкхолдеры

| Роль | Организация | Ключевые лица | Как себя ведут |
|------|-------------|---------------|----------------|
| Генподрядчик | <Org A> | <кто> | <паттерн поведения на встречах> |
| Субподрядчик | <Org B> | <кто> | <напр.: «играют в молчанку, выходят в удобной позиции»> |
| Функциональный заказчик | <Org C> | <кто> | <союзник/оппонент, история> |

## Ключевые проблемы (актуальный список)

1. <проблема + почему это проблема>
2. <проблема>
3. <проблема>

## Консенсус-позиции (что уже согласовано между исполнителями)

1. <позиция — например, какие пункты ТЗ признаны невыполнимыми и почему>

## Как готовить пользователя к совещанию

- Составь карту: кто будет, чего хочет каждый, где конфликты.
- Подготовь 3 сценария: оптимистичный / рабочий / «всё пошло не так».
- Сформулируй 2–3 фразы-заготовки для острых моментов (пользователь склонен
  к прямоте, которая читается как агрессия — заготовки снижают риск).
- После совещания: разбор транскрипта → обновить этот skill (таблицу
  стейкхолдеров и список проблем).

## Файлы проекта

```
<корневая папка проекта>/
├── ТЗ/               ← версии ТЗ и правки заказчика
├── Совещания/        ← транскрипты и разборы
├── Отчётность/       ← этапные отчёты
└── AGENTS.md         ← контекст папки (этот skill — поверх него)
```
