## Что такое URDF?
- **URDF** (Unified Robot Description Format) — XML-формат для описания **кинематической и визуальной структуры робота**.
- Используется в [[Ros2]] для:
  - Визуализации в `RViz`
  - Симуляции в `Gazebo` / `Ignition` / `Webots`
  - Планирования движений (`MoveIt`)
  - Кинематических расчётов

> ⚠️ URDF описывает **статическую** структуру. Для динамики и сложных сочленений (например, параллельных механизмов) используется **SRDF** или **SDF**.

---
ПРИМЕР типичного urdf файла
```
<robot>
	<link>
		...
	</link>
	<link>
		...
	</link>
	<joint>
		...
	</joint>
</robot>
```


## Основные элементы URDF

### `<robot>`
Корневой тег. Обязательный атрибут: `name`.

```xml
<robot name="my_robot">
  <!-- links, joints и т.д. -->
</robot>
```

### `<link>`
Описывает твердое тело (часть робота): визуальное, коллизионное и инерционное представление.

Каждый `<link>` содержит три основных подраздела:
1. `<visual>`
	Определяет визуальное представление (для RViz, симуляторов).
	Поддерживаемые геометрии:
	- `<box size="X Y Z"/>`
	- `<cylinder length="L" radius="R"/>`
	- `<sphere radius="R"/>`
	- `<mesh filename="file://$(find package)/meshes/base.stl" scale="sx sy sz"/>`

``` XML
<visual>
  <geometry>
    <mesh filename="file://$(find my_robot_description)/meshes/chassis.stl" scale="1 1 1"/>
  </geometry>
  <material name="blue">
    <color rgba="0 0 1 1"/>
  </material>
</visual>
```

	
2. `<collision>`	
	Определяет физические границы для столкновений (в симуляторах). Часто упрощённая версия `<visual>`.		
	
``` XML
<collision>
  <geometry>
    <box size="0.5 0.3 0.1"/>
  </geometry>
</collision>
```
	
3. `<inertial>`
	Задаёт массу и момент инерции — важно для физики и управления.
	
``` XML
<inertial>
  <mass value="1.0"/>
  <inertia ixx="0.01" ixy="0" ixz="0" iyy="0.01" iyz="0" izz="0.01"/>
</inertial>
```

Общий пример:
``` XML
<link name="base_link">
  <visual>
    <geometry><box size="0.1 0.1 0.1"/></geometry>
  </visual>
  <collision>
    <geometry><box size="0.1 0.1 0.1"/></geometry>
  </collision>
  <inertial>
    <mass value="1.0"/>
    <inertia ixx="0.01" ixy="0" ixz="0" iyy="0.01" iyz="0" izz="0.01"/>
  </inertial>
</link>
```

### `<joint>`

Описывает соединение между двумя link'ами.
#### Типы:
- revolute — вращательный (ограниченный угол)
- continuous — вращательный (без ограничений)
- prismatic — поступательный
- fixed — жёсткое соединение (часто для сенсоров)
- floating, planar — редко используются
  
Обязательные элементы:
`<parent link="..."/>` и `<child link="..."/>`

Связь между двумя линками. 
`<origin xyz="x y z" rpy="r p y"/>`

Позиция и ориентация относительно родительского link.
`<axis xyz="ax ay az"/>`

Направление оси вращения или перемещения (для revolute/prismatic).
`<limit lower="..." upper="..." effort="..." velocity="..."/>`

``` XML
<joint name="shoulder" type="revolute">
  <parent link="base_link"/>
  <child link="arm_link"/>
  <origin xyz="0 0 0.2" rpy="0 0 0"/>
  <axis xyz="0 1 0"/>
  <limit lower="-1.57" upper="1.57" effort="10" velocity="1.0"/>
</joint>
```

#### Как использовать URDF в ROS 2 ???
[[Launch]] файлы

