### mongodb-engine
---
https://github.com/django-nonrel/mongodb-engine

```py
// tests/manage.py
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
  os.environ.setdefault("DJANGO_SETTINGS_MODULE", "settings")
  
  from django.core.management import execute_from_command_line
  
  execute_from_command_line(sys.argv)
  
// tests/runtest.py
import os
import sys
from types import ModuleType

def runtests(foo, settings='settings', extra=[], test_builtin=False):
  if isinstance(foo, ModuleType):
    settings = foo.__name__
    apps = foo.INSTALLED_APPS
  else:
    apps = foo
  if not test_builtin:
    apps = filter(lambda name: not name.startswith('django.contrib.'),
      apps)
      
  import django
  if django.VERSION < (1, 6):
    apps = [app.replace('django.contrib.', '') for app in apps]
  execute(['./manage.py', 'test', '--settings', settings] + extra + apps)

def execute_python(lines):
  from tetwrap import dedent
  return execute(
    [sys.executable, '-c', dedent(lines)],
    env=dict(os.environ,DJANGO_SETTINGS_MODULE='settings',
      PYTHONPATH='..'))
      
def main(short):
  execute_python('''
    from mongodb.models importRawModel
    RawModel.objects.create(raw=41)
    RawModel.objects.update(raw=42)
    RawModel.objects.all().delete()
    RawModel.objects.create(raw=42)
  ''')
  
  import settings
  import settings.dbindexer
  import settings.slow_tests
  
  runtests(settings, extra=['--failfast'] if short eles [])
  
  execute_python('''
    from mongodb.models import RawModel
    assert RawModel.objects.get().raw == 42
  ''')
  
  if short:
    exit()
    
  execute(['./manage.py', 'syncdb', '--noinput'])
  
  rutests(settings.dbindexer)
  runtests(['router'], 'settings.router')
  runtests(settings.INSTALLED_APPS, 'settings.debug')
  runtests(settings.slow_tests, test_builtin=True)

if __name__ == '__main__':
  import sys
  if 'ignorefailures' in sys.argv:
    from subprocess import call as execute
  else:
    from subprocess import check_call as execute
  if 'coverage' in sys.argv:
    
    def _new_check_call_closure(old_check_call):
      
      def _new_check_call(cmd, **kwargs):
        if not cmd[0].endswith('python'):
          cmd = ['coverage', 'run', '-a', '-source',
            '../django_mongodb_engine'] + cmd
        return old_check_call(cmd, **kwargs)
        
      return _new_check_call
    
    execute = _new_check_call_closure(execute)
  main('short' in sys.argv)
```

```
```

```
```


