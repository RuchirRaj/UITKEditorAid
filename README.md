# UITK Editor Aid
Elements and scripts that help in making Unity editors with UIToolkit.<br/>

[UIToolkit](https://docs.unity3d.com/Manual/UIElements.html) allows for interfaces that are more dynamicand more
performant than [IMGUI](https://docs.unity3d.com/Manual/GUIScriptingGuide.html). Its web-like API makes creating 
complex Editor UI (i.e. node graphs) a lot easier. Yet, its editor related API is currently lacking the features
needed to make using it straight forward. This package contains some of the stuff I use to solve that problem.
<br/><br/>


## Some of the stuff included
Click a name to go to the relevant documentation page for usage info and some code examples:

### [EditableLabel](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.EditableLabel.html)
A label that tranforms into a text field to be edited. It's edited with a double-click by default.<br/>
![EditableLabel preview](doc_images/EditableLabel.png)

### [ArrayPropertyField](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ArrayPropertyField.html)
A UITK version of the good old reorderable list, very customizable. Here's what it looks like by default:

![ArrayPropertyField preview](doc_images/DefaultReorderableList.png)

There's also an abstract [ListControl](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ListControl.html)
class that you can use to create your own lists that don't depend on a SerializedProperty
 
### [ManagedReferenceField](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ManagedReferenceField.html)
Currently, PropertyFields from members with the [SerializeReference](https://docs.unity3d.com/ScriptReference/SerializeReference.html)
attribute break when they change type. This element is like a PropertyField that updates when the type changes.<br/>
The next gif shows a customized ArrayPropertyField that uses ManagedReferenceFields for its items. Notice that the
interface updates itself when the elements change type due to being reordered:

![A customized list of Managed References](doc_images/ManagedRefsList.png)

There's also a [ManagedReferenceTypeTracker](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ManagedReferenceTypeTracker.html)
that can be used for more low level stuff.

### [Rebinder](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.Rebinder.html)
There are things that need `VisualElement.Bind(serializedObject)` to be called to be updated in UIToolkit. For example,
items that are added to a list, or fields with the `[SerializeReference]` attribute that change type. The problem is
that each element that is bound separately has an important performance cost. The Rebinder element solves that by
binding the whole hierarchy every time an update is needed. It also throttles rebinding requests, putting together 
multiple update calls for better performance. 

Relevant elements in this library already use it: ArrayPropertyFields have better performance when they are inside a
Rebinder, and elements related to ManagedReferences need to be inside a Rebinder to work.

You can use the Rebinder yourself by calling the
[RequestRebind](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.Rebinder.html#ArteHacker_UITKEditorAid_Rebinder_RequestRebind)
method, or by implementing the
[IRebindingTrigger](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.IRebindingTrigger.html) interface
in your elements, if you are up for more advanced stuff.

### [ValueTracker](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ValueTracker-1.html)
Some times you don't need a VisualElement for anything visual, sometimes you just need a quick way to get a callback when
a property changes. This element helps you with that.

### [ListOfInspectors](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.ListOfInspectors.html)
A list that's similar to the component list in GameObjects. I use it with ScriptableObject subassets. You still have to
do the stuff that's not related to UIToolkit yourself, like handling assets and the lifetime of your objects, which is
outside the scope of this package; but if you know how to do that, this could be very helpful.

![ListOfInspectors preview](doc_images/ListOfInspectors.png)
<br/>

### [More Stuff](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.html)
Click the link to go to the docs homepage. There is stuff to replace some common IMGUI methods, like a 
[Disabler](https://artehacker.com/UITKEditorAid/api/ArteHacker.UITKEditorAid.Disabler.html) element that's equivalent
to `EditorGUI.DisabledScope`, and `FixedSpace`/`FlexibleSpace` elements equivalent to GUILayout's `Space` and `FlexibleSpace`.
There are also some extension methods that I've found useful for UIToolkit editor development, and some UITK manipulators.
<br/><br/>

## A caveat
Currently all elements that need rebinding, like Lists and ManagedReferenceFields, don't support UXML. That's because
they need to get a `SerializedProperty` or a `SerializedObject` in their constructor. The correct way to solve that would
be to obtain them with Unity's binding system, but the required API is not public yet. I don't want to use reflection
because that API seems likely to change, and editors that break when doing a minor Unity update are awful.<br/><br/>

## IMPORTANT: Avoid collisions when including this in packages and plugins
Sometimes one needs to copy a library inside a Unity plugin, that's very ok, that's what the MIT license is for. If you
do that, please take steps to avoid collisions when your users have also installed this package themselves. The easiest
way to do it is:

1. Rename the ArteHacker part of the namespace. Most text editors have an automated way of doing that.

2. Delete the .asmdef file inside the Editor folder. Create a new one if you need it; renaming it is not enough because
some users reference them as assets (with their GUID).