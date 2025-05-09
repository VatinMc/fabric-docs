---
title: Создание вашего первого блока
description: Научитесь создавать собственные блоки в Minecraft.
authors:
  - IMB11
---

Блоки являются основой строительства в Minecraft. Как и всё здесь, блоки хранятся в реестрах.

## Подготовка класса вашего блока {#preparing-your-blocks-class}

Если вы прошли туториал [Создание первого предмета](../items/first-item), этот процесс будет вам хорошо знаком. Необходимо создать метод для регистрации блока и предмета блока.

Поместите этот метод в класс `ModBlocks` (или как вы его назовете).

Что-то подобное Mojang делают с ванильными блоками. Вы можете обратиться к классу `Blocks`, чтобы увидеть, как они это делают.

@[code transcludeWith=:::1](@/reference/1.21/src/main/java/com/example/docs/block/ModBlocks.java)

Как и в случае с предметами, необходимо убедиться, что класс загружен, чтобы все статические поля с экземплярами ваших блоков были инициализированы.

Это можно сделать, создав пустой метод `initialize`, который может быть вызван в инициализаторе вашего мода, чтобы запустить статическую инициализацию.

:::info
Если вы не знаете, что такое статическая инициализация, это процесс инициализации статических полей в классе. Она выполняется, когда JVM загружает класс, и завершается до создания какого-либо экземпляра класса.
:::

```java
public class ModBlocks {
    // ...

    public static void initialize() {}
}
```

@[code transcludeWith=:::1](@/reference/1.21/src/main/java/com/example/docs/block/FabricDocsReferenceBlocks.java)

## Создание и регистрация вашего блока {#creating-and-registering-your-block}

Подобно предметам, блоки имеют в своем конструкторе класс `Blocks.Settings`, который определяет такие свойства блока как его звуковые эффекты и уровень добычи.

Мы не будем рассматривать здесь все настройки, вы можете сами заглянуть в класс, чтобы увидеть их варианты, которые говорят сами за себя.

Для примера мы создадим простой блок, который имеет свойства земли, но представляет другой материал.

:::tip
Вы можете использовать `AbstractBlock.Settings.copy(AbstractBlock block)`, чтобы скопировать параметры существующего блока. В данном случае мы могли бы использовать `Blocks.DIRT` для копирования параметров блока земли, но для примера мы используем конструктор.
:::

@[code transcludeWith=:::2](@/reference/1.21/src/main/java/com/example/docs/block/ModBlocks.java)

Для автоматического создания предмета блока мы можем передать `true` параметру `shouldRegisterItem` метода `register`, который мы создали на предыдущем шаге.

### Добавление вашего блока в группу предметов {#adding-your-block-to-an-item-group}

Поскольку `BlockItem` был создан и зарегистрирован автоматически, для добавления его в группу элементов необходимо использовать метод `Block.asItem()` для получения экземпляра `BlockItem`.

Для этого примера мы используем пользовательскую группу предметов, созданную на странице [Собственные вкладки предметов](../items/custom-item-groups).

@[code transcludeWith=:::3](@/reference/1.21/src/main/java/com/example/docs/block/ModBlocks.java)

После этого ваш блок появится в творческом инвентаре, и вы сможете разместить его в мире!

![Блок без модели и текстуры](/assets/develop/blocks/first_block_0.png)

Осталось несколько проблем: предмет блока не имеет названия, а блок не имеет текстуры, модели блока и модели предмета.

## Добавление переводов блоков {#adding-block-translations}

Чтобы добавить перевод, необходимо создать ключ перевода в файле перевода - `assets/mod-id/lang/en_us.json`.

Minecraft будет использовать этот перевод в творческом инвентаре и других местах, где отображается название блока, например, в качестве ответе команды.

```json
{
    "block.mod_id.condensed_dirt": "Condensed Dirt"
}
```

Вы можете перезапустить игру или создать свой мод и нажать <kbd>F3</kbd>+<kbd>T</kbd>, чтобы применить изменения. После этого вы увидите, что у блока есть имя в творческом инвентаре и других местах, например на экране статистики.

## Модели и текстуры {#models-and-textures}

Все текстуры блоков можно найти в папке `assets/mod-id/textures/block` - пример текстуры для блока «Condensed Dirt» доступен для бесплатного использования.

<DownloadEntry type="Texture" visualURL="/assets/develop/blocks/first_block_1.png" downloadURL="/assets/develop/blocks/first_block_1_small.png" />

Чтобы текстура отображалась в игре, необходимо создать блок и модель предмета, которые можно найти в соответствующих местах для блока «Condensed Dirt»:

- `assets/mod-id/models/block/condensed_dirt.json`
- `assets/mod-id/models/item/condensed_dirt.json`

Модель элемента довольно проста, она может просто использовать модель блока в качестве родительской, поскольку большинство моделей блоков поддерживают визуализацию в графическом интерфейсе:

@[code](@/reference/1.21/src/main/resources/assets/fabric-docs-reference/models/item/condensed_dirt.json)

Однако в нашем случае модель блока должна быть родительской для модели `block/cube_all`:

@[code](@/reference/1.21/src/main/resources/assets/fabric-docs-reference/models/block/condensed_dirt.json)

При загрузке игры вы можете заметить, что текстура по-прежнему отсутствует. Это связано с тем, что вам необходимо добавить определение состояния блока.

## Создание определения состояния блока {#creating-the-block-state-definition}

Определение blockstate используется для указания игре, какую модель следует визуализировать на основе текущего состояния блока.

Для примера блок, который не имеет сложного состояния, в определении требуется только одна запись.

Этот файл должен находиться в папке `assets/mod_id/blockstates`, а его имя должно совпадать с идентификатором блока, использованным при регистрации вашего блока в классе `ModBlocks`. Например, если идентификатор блока — `condensed_dirt`, файл должен называться `condensed_dirt.json`.

@[code](@/reference/1.21/src/main/resources/assets/fabric-docs-reference/blockstates/condensed_dirt.json)

Состояния блоков действительно сложны, поэтому они рассматриваются на следующей странице: [Состояния блоков](./blockstates)

Перезапустите игру или перезагрузите только assets с помощью <kbd>F3</kbd>+<kbd>T</kbd>, чтобы применить изменения. Вы должны будете увидеть текстуру блока в инвентаре и в мире:

![Block in world with suitable texture and model](/assets/develop/blocks/first_block_4.png)

## Добавление выпадения для блоков {#adding-block-drops}

При разрушении блока в режиме выживания вы можете увидеть, что блок не выпадает. Возможно для этого вам понадобится эта функция, однако, чтобы блок выпадал как предмет при разрушении, необходимо реализовать его таблицу добычи. Файл таблицы добычи должен быть помещен в папку `data/mod-id/loot_table/blocks/`.

:::info
Для более глубокого понимания таблиц добычи вы можете обратиться к официальной странице [Minecraft Wiki - Loot Tables](https://minecraft.wiki/w/Loot_table).
:::

@[code](@/reference/1.21/src/main/resources/data/fabric-docs-reference/loot_tables/blocks/condensed_dirt.json)

В таблице добычи указано, какой предмет выпадет из блока, когда блок сломан и когда он взорван взрывом.

## Рекомендация по выбору инструмента для сбора {#recommending-a-harvesting-tool}

Вы также можете захотеть, чтобы ваш блок можно было собирать только определенным инструментом — например, вы можете захотеть ускорить сбор с помощью лопаты.

Все теги инструментов должны быть помещены в папку `data/minecraft/tags/block/mineable/`, где имя файла зависит от типа используемого инструмента, одного из следующих:

- `hoe.json`
- `axe.json`
- `pickaxe.json`
- `shovel.json`

Содержимое файла довольно простое — это список элементов, которые следует добавить в теге.

В этом примере, блок «Condensed Dirt» добавляется к тегу `shovel`.

@[code](@/reference/1.21/src/main/resources/data/minecraft/tags/mineable/shovel.json)

## Уровни добычи {#mining-levels}

Аналогично тег уровня добычи можно найти в той же папке, он имеет следующий формат:

- `needs_stone_tool.json` - Минимальный уровень: каменные инструменты
- `needs_iron_tool.json` - Минимальный уровень: железные инструменты
- `needs_diamond_tool.json` - Минимальный уровень: алмазные инструменты.

Файл имеет тот же формат, что и файл инструмента для сбора — список элементов, которые необходимо добавить в теге.

## Дополнительные заметки {#extra-notes}

Если вы добавляете в свой mod несколько блоков, вы можете рассмотреть возможность использования [Генерации данных](https://fabricmc.net/wiki/tutorial:datagen_setup) для автоматизации процесса создания моделей блоков и предметов, определений состояний блоков и таблиц добычи.
