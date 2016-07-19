# Display the result from the JumpScript on your wiki page

As we did before we will now create a macro that calls the actor, this time returning a table listing all hostnames.

Let's create a new macro for this:

```
cd /opt/jumpscale7/apps/portals/main/base/demo/.macros/wiki/
mkdir displayHostNames
cd displayHostNames
vi 1_displayHostNames.py
```

Here the code:
```
def main(j, args, params, tags, tasklet):

    # code here
    res = j.apps.bootcamp.mysecondactor.getHostNames()
    host = []
    for item in res:
        host.append(item.result)

    out = ','.join (host)

    params.result = (out, args.doc)
    return params
```

Add the macro to Page1:
```
{{displayHostNames}}
```

Restart the portal:
```
ays restart -n portal
```

Test the result:
```
http://PORTAL_IP:PORTAL_PORT/demo/page1?testname=yves&x=1&y=2
```
