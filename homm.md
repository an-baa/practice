# HoMM

## 1.Описание предметной области и сущностей:
Player: может быть жив или мертв.С уникальным идентификатором Id и имеет набор методов для обработки результатов взаимодействия(CanBeat сопоставляет сиды игрока с армией, Consume усваивает полученные ресурсы, Die вызывается в случае поражения)
Dwelling(жилье): это мирный или нейтральный объект, который можно захватить с помощью IToOwn.
Mine(шахта): охраняется армией с помощью IToDefend, и также с нее можно собрать сокровища с IToLoot и захватить ее с помощью IToOWn.
Creep(лагерь с монстрами): захищает сокровища с помощью IToDefend и после победы игрок получает награду с помощью IToLoot.
Wolves(волки): имеют армию для сражения с помощью IToDefend.
ResourcePile(куча ресурсов): мирная территория, с которйой можно забрать сокровища с помощью IToLoot.
Interaction с Mаke: сначала проверяет IToDefend.Если бой выигран или его не было, то срабатывают IToLoot и IToOwn.Если проигран, то игрок умирает.
## 2.Диаграмма классов:

```mermaid

classDiagram
    class IMapObject {
        <<interface>>
    }
    class IToDefend {
        <<interface>>
    }
    class IToLoot {
        <<interface>>
    }
    class IToOwn {
        <<interface>>
        +int Owner
    }

    class Army {
        <<Сущность>>
    }
    class Treasure {
        <<Сущность>>
    }

    class Dwelling {
        +int Owner
    }
    class Mine {
        +int Owner
        +Army Army
        +Treasure Treasure
    }
    class Creeps {
        +Army Army
        +Treasure Treasure
    }
    class Wolves {
        +Army Army
    }
    class ResourcePile {
        +Treasure Treasure
    }

    class Player {
        +int Id
        +CanBeat(Army army) bool
        +Consume(Treasure treasure) void
        +Die() void
    }

    class Interaction {
        +Make(Player player, object mapObject) void$
    }

    IMapObject <|.. Dwelling : размещается на карте
    IMapObject <|.. Mine : размещается на карте
    IMapObject <|.. Creeps : размещается на карте
    IMapObject <|.. Wolves : размещается на карте
    IMapObject <|.. ResourcePile : размещается на карте

    IToOwn <|.. Dwelling : можно захватить (задать Owner)
    IToDefend <|.. Mine : охраняется армией
    IToLoot <|.. Mine : содержит сокровища
    IToOwn <|.. Mine : можно захватить (задать Owner)
    IToDefend <|.. Creeps : охраняется армией
    IToLoot <|.. Creeps : содержит сокровища
    IToDefend <|.. Wolves : охраняется армией
    IToLoot <|.. ResourcePile : содержит сокровища

    IToDefend --> Army : предоставляет доступ к защитникам
    IToLoot --> Treasure : предоставляет доступ к добыче

    Interaction ..> IMapObject : опрашивает интерфейсы объекта
    Interaction ..> Player : убивает или выдает награду
```