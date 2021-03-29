# Django errors
## Migration errors
#### CircularDependencyError

Your 2 migrations have a dependency on each other. Look at the generated migration for your account app and you will probably see something like this

```python
class Migration(migrations.Migration):

    dependencies = [
        ('projects', '0001_initial'),
    ]
```
There is probably something similar in your projects migration

You need to delete these two migration files and run makemigrations again without specifying an app name

[Link](https://stackoverflow.com/questions/45309128/circular-dependency-error-in-django)

