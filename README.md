## Оглавление
1. [Режим симуляции](#режим-симуляции)  
2. [Основы интерфейса и скриптинга](#основы-интерфейса)  
3. [Автоматизация работы крипов](#автоматизация-крипов)  
4. [Улучшение контроллера](#улучшение-контроллера)  
5. [Строительство структур](#строительство-структур)  
6. [Защита комнаты](#защита-комнаты)  
7. [Что дальше?](#что-дальше)  

---

<a name="режим-симуляции"></a>
## 1. Режим симуляции 🖥️
**Зачем нужен:**  
- Отладка кода локально.
- Пауза/перезапуск без последствий.

**Ссылки:**  
- [Скрипты для учебника (GitHub)](https://github.com/TheKilloboy/Screeps-Tutorial-JavaScript-Coding-Basics/tree/main/tmp)
- [Базовый курс JavaScript](https://www.codecademy.com/learn/learn-javascript)

---

<a name="основы-интерфейса"></a>
## 2. Основы интерфейса и скриптинга 🛠️

### Создание первого крипа
```js
// Консоль (одноразовый код)
Game.spawns['Spawn1'].spawnCreep([WORK, CARRY, MOVE], 'Harvester1');
```
**Пояснение:**  
- `WORK` — добыча энергии.  
- `CARRY` — перенос ресурсов.  
- `MOVE` — передвижение.  

### Автоматизация сбора энергии
```js
// Скрипт (выполняется каждый тик)
module.exports.loop = () => {
  const creep = Game.creeps['Harvester1'];
  const source = creep.room.find(FIND_SOURCES)[0];
  
  if (creep.store.getFreeCapacity() > 0) {
    if (creep.harvest(source) === ERR_NOT_IN_RANGE) {
      creep.moveTo(source, { visualizePathStyle: { stroke: '#FFAA00' } });
    }
  } else {
    const target = creep.room.controller;
    if (creep.upgradeController(target) === ERR_NOT_IN_RANGE) {
      creep.moveTo(target);
    }
  }
};
```
**Совет:** Используйте `creep.say('🚜')` для визуализации действий.

---

<a name="автоматизация-крипов"></a>
## 3. Автоматизация работы крипов 🤖

### Очистка памяти
```js
for (const name in Memory.creeps) {
  if (!Game.creeps[name]) delete Memory.creeps[name];
}
```

### Автоспавн сборщиков
```js
const harvesters = _.filter(Game.creeps, c => c.memory.role === 'harvester');
if (harvesters.length < 2) {
  const newName = `Harvester_${Game.time}`;
  Game.spawns.Spawn1.spawnCreep([WORK, CARRY, MOVE], newName, {
    memory: { role: 'harvester' }
  });
}
```

---

<a name="улучшение-контроллера"></a>
## 4. Улучшение контроллера ⬆️

### Модуль для улучшателя
```js
// role.upgrader.js
const roleUpgrader = {
  run: (creep) => {
    if (creep.store[RESOURCE_ENERGY] === 0) {
      const source = creep.room.find(FIND_SOURCES)[0];
      if (creep.harvest(source) === ERR_NOT_IN_RANGE) {
        creep.moveTo(source);
      }
    } else {
      if (creep.upgradeController(creep.room.controller) === ERR_NOT_IN_RANGE) {
        creep.moveTo(creep.room.controller);
      }
    }
  }
};
module.exports = roleUpgrader;
```

---

<a name="строительство-структур"></a>
## 5. Строительство структур 🏗️

### Модуль для строителя
```js
// role.builder.js
const roleBuilder = {
  run: (creep) => {
    if (creep.memory.building && creep.store[RESOURCE_ENERGY] === 0) {
      creep.memory.building = false;
      creep.say('🔄 Сбор');
    }
    if (!creep.memory.building && creep.store.getFreeCapacity() === 0) {
      creep.memory.building = true;
      creep.say('🚧 Стройка');
    }
    if (creep.memory.building) {
      const target = creep.room.find(FIND_CONSTRUCTION_SITES)[0];
      if (target) {
        if (creep.build(target) === ERR_NOT_IN_RANGE) {
          creep.moveTo(target);
        }
      }
    } else {
      const source = creep.room.find(FIND_SOURCES)[0];
      if (creep.harvest(source) === ERR_NOT_IN_RANGE) {
        creep.moveTo(source);
      }
    }
  }
};
module.exports = roleBuilder;
```

---

<a name="защита-комнаты"></a>
## 6. Защита комнаты 🛡️

### Башня: атака и ремонт
```js
const tower = Game.getObjectById('TOWER_ID');
if (tower) {
  const enemy = tower.pos.findClosestByRange(FIND_HOSTILE_CREEPS);
  if (enemy) tower.attack(enemy);
  
  const damagedWall = tower.pos.findClosestByRange(FIND_STRUCTURES, {
    filter: s => s.hits < s.hitsMax && s.structureType === STRUCTURE_WALL
  });
  if (damagedWall) tower.repair(damagedWall);
}
```

---

<a name="что-дальше"></a>
## 7. Что дальше? 🚀

### Чек-лист для развития:
- [ ] Построить 5 расширений.  
- [ ] Создать крипов с улучшенным телом (например, `[WORK,WORK,CARRY,MOVE,MOVE]`).  
- [ ] Настроить автоматическую защиту от NPC-вторжений.  
- [ ] Освоить межкомнатные перемещения.

### Полезные ресурсы:
- [Официальная документация](http://docs.screeps.com/)  
- [Форум сообщества](https://screeps.com/forum/)  
- [Discord-сервер](http://chat.screeps.com/)  

---

**Успехов в освоении Screeps!** 🌟  
Ваша колония ждет великих свершений.
