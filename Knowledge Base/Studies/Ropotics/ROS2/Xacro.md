**Xacro** это расширенное [[URDF]], которое более читаемое и позволяет щаписывать параметры в переменные. 

Как следует из названия, xacro — это язык макросов для XML. Программа xacro выполняет все макросы и выводит результат. Типичный пример использования выглядит примерно так:

``` bash
xacro model.xacro > model.urdf
```

Вы также можете автоматически генерировать urdf-файл в файле запуска. Это удобно, поскольку он остаётся актуальным и не занимает место на жёстком диске. Однако его генерация занимает время, поэтому имейте в виду, что запуск файла запуска может занять больше времени.

``` launch.py
path_to_urdf = get_package_share_path('robot_description') / 'robots' / 'pr2.urdf.xacro'
robot_state_publisher_node = launch_ros.actions.Node(
    package='robot_state_publisher',
    executable='robot_state_publisher',
    parameters=[{
        'robot_description': ParameterValue(
            Command(['xacro ', str(path_to_urdf)]), value_type=str
        )
    }]
)
```

## Константы

Xacro позволяет указывать свойства, которые действуют как константы.

``` xacro
Задать переменную:
<xacro:property name=”robotname” value=”marvin” />

Использовать:
<link name=”${robotname}s_leg” />
```

## Макросы

Вот самый большой и полезный компонент пакета xacro.

### простой макрос

Давайте рассмотрим простой бесполезный макрос.

``` xacro
<xacro:macro name="default_origin">
    <origin xyz="0 0 0" rpy="0 0 0"/>
</xacro:macro>
<xacro:default_origin />
```

(Это бесполезно, поскольку если источник не указан, он имеет то же значение, что и this.) Этот код сгенерирует следующее.

``` xacro
<origin rpy="0 0 0" xyz="0 0 0"/>
```

- Технически имя не является обязательным элементом, но его необходимо указать, чтобы иметь возможность его использовать.

- Каждый экземпляр заменяется содержимым тега.`<xacro:$NAME />``xacro:macro`

- Обратите внимание, что даже если это не совсем то же самое (два атрибута поменялись местами), сгенерированный XML эквивалентен.

- Если xacro с указанным именем не найден, он не будет развернут и НЕ вызовет ошибку.

### Параметризованный макрос

Вы также можете параметризовать макросы, чтобы они не генерировали каждый раз один и тот же текст. В сочетании с математическими функциями это ещё более эффективно.

Для начала рассмотрим пример простого макроса, используемого в R2D2.

``` xacro
<xacro:macro name="default_inertial" params="mass">
    <inertial>
            <mass value="${mass}" />
            <inertia ixx="1e-3" ixy="0.0" ixz="0.0"
                 iyy="1e-3" iyz="0.0"
                 izz="1e-3" />
    </inertial>
</xacro:macro>
```

Это можно использовать с кодом
``` xacro
<xacro:default_inertial mass="10"/>
```
Параметры действуют так же, как свойства, и их можно использовать в выражениях.

Вы также можете использовать целые блоки в качестве параметров.
``` xacro
<xacro:macro name="blue_shape" params="name *shape">
    <link name="${name}">
        <visual>
            <geometry>
                <xacro:insert_block name="shape" />
            </geometry>
            <material name="blue"/>
        </visual>
        <collision>
            <geometry>
                <xacro:insert_block name="shape" />
            </geometry>
        </collision>
    </link>
</xacro:macro>

<xacro:blue_shape name="base_link">
    <cylinder radius=".42" length=".01" />
</xacro:blue_shape>
```
- Чтобы указать параметр блока, поставьте звездочку перед именем его параметра.
- Блок можно вставить с помощью команды insert_block.
- Вставляйте блок столько раз, сколько захотите.
