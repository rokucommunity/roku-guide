# Singleton
For some use-cases you want to always have the same instance of a component. F.e. when using app wide repositories or managers.
I suggest to register all app wide managers, tasks, repositories, etc. in the interface of the scene and create them in the init.

```
<!--- Scene xml --->
<interface>
    <field id="myManager" type="node">
</interface>
```
```
' Scene brs
function Init()
    m.top.myManager = Create("roSGNode", "MyManager")
end function

```
You can access your scene from all over the app like this:
```
' any component brs
m.top.getScene()
```


# Global Scope
The m.global scope on the other hand is great for storing simple values like colors, the current device resultion, version and more.
It should not be used to add anything more than strings, boolean or integers.
