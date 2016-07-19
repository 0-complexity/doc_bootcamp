# Calling an actor via a macro

Let's create a new macro:

```
cd /opt/jumpscale7/apps/portals/main/base/demo/.macros/wiki/
mkdir anothermacro
cd anothermacro
vi 1_anothermacro.py
```

Here the code:
```
def main(j, args, params, tags, tasklet):
    x = int(args.getTag('x'))
    y = int(args.getTag('y'))

    out = str(j.apps.bootcamp.myfirstactor.add(x, y))
    params.result = (out, args.doc)
    return params
```

Add the macro to Page1:
```
{{anothermacro x:$$x y:$$y}}
```

Test the result:
```
http://PORTAL_IP:PORTAL_PORT/demo/page1?testname=yves&x=1&y=2
```

![](AnotherMacro.png)
