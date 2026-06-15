# HoMM

## 1.Описание предметной области и сущностей:
Player: может быть жив или мертв, уникальным идентификатором Id и имеет набор методов для обработки результатов взаимодействия(CanBeat сопоставляет сиды игрока с армией, Consume усваивает полученные ресурсы, Die вызывается в случае поражения)
Dwelling(жилье): это мирный или нейтральный объект, который можно захватить с помощью IToOwn.
Mine(шахта): охраняет армией с помощью IToDefend, и также в можно собрать сокровища с IToLoot и захватить ее с помощью IToOWn.
Creep(лагерь с монстрами): захищает сокровища с помощью IToDefend и после победы игрок получает награду с помощью IToLoot.
Wolves(волки): имеют армию для сражения с помощью IToDefend.
ResourcePile(куча ресурсов): мирная территория, с которйой можно забрать сокровища с помощью IToLoot.
Interaction с Mаke: сначала проверяет IToDefend.Если бой выигран или его не было, то срабатывают IToLoot и IToOwn.Если проигран, то игрок умирает.
## 2.Диаграмма классов:

```mermaid

classDiagram
    class IToDefend {
        <<interface>>
        +Army Army
    }
    class IToLoot {
        <<interface>>
        +Treasure Treasure
    }
    class IToOwn {
        <<interface>>
        +int Owner
    }

    class Player {
        +int Id
        +CanBeat(Army army) bool
        +Die()
        +Consume(Treasure treasure)
    }

    class Interaction {
        +Make(Player player, object mapObject)$
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

    Dwelling ..|> IToOwn
    Mine ..|> IToDefend
    Mine ..|> IToLoot
    Mine ..|> IToOwn
    Creeps ..|> IToDefend
    Creeps ..|> IToLoot
    Wolves ..|> IToDefend
    ResourcePile ..|> IToLoot
    
    Interaction ..> Player : управляет состоянием
    Interaction ..> IToDefend : проверяет защиту
    Interaction ..> IToLoot : передает ресурсы
    Interaction ..> IToOwn : меняет владельца
    
    Player ..> Army : оценивает силу
    Player ..> Treasure : пополняет инвентарь
```