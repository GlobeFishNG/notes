# HELP

## UML

Using [PlantUML](https://github.com/GitbookIO/plugin-puml) to draw UML.

* SVG output. (Friendly to scaling)

```
{% plantuml format = 'svg' %}
Alice -> Bob: Hello, Bob
Alice <-- Bob: Hello, Alice
{% endplantuml %}
```

{% plantuml format = 'svg' %}
Alice -> Bob: Hello, Bob
Alice <-- Bob: Hello, Alice
{% endplantuml %}

* PNG output.

```
{% plantuml format = 'png' %}
Alice -> Bob: Hello, Bob
Alice <-- Bob: Hello, Alice
{% endplantuml %}
```

{% plantuml format = 'png' %}
Alice -> Bob: Hello, Bob
Alice <-- Bob: Hello, Alice
{% endplantuml %}

## Check Box

```bash
- [ ] Uncompleted task.
- [x] Completed task.
```

- [ ] Uncompleted task.
- [x] Completed task.
