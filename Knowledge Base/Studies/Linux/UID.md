Идентификатор [[Пользователь|пользователя]].
Определенные номера UID и диапазоны номеров используются для определенных целей.

- UID 0 всегда присваивается учетной записи суперпользователя root.

- UID 1–200 — это диапазон «системных пользователей» статически назначенных системным процессам.

- UID 201-999 — это диапазон «системных пользователей», используемых системными процессами, которым не принадлежат файлы в файловой системе. Обычно они назначаются динамически из доступного пула при установке программного обеспечения, которое в них нуждается. Программы запускаются от имени этих «непривилегированных» пользователей системы, чтобы ограничить их доступ только к тем ресурсам, которые им необходимы для функционирования.

- UID 1000+ — диапазон, доступный для назначения обычным пользователям.