# Associative Arrays and Nodes
rokudevelopers.slack.com: @Scott Brower

Working mainly on a component library, where I don’t control program flow and have to store persistent information as global data, I have found (and have been shown) that there are certain advantages and disadvantages to using Associative Arrays vs roSGNodes of fields.

Associative arrays are extremely flexible but not very performant under certain circumstances.  Items can be of course added and deleted as needed, without the exact key definition being defined or even known during development.  But they are a value type unlike many other languages.  If an AA crosses any type of boundary, be it a task, or being stored to or from the Global array, it seems it is fully serialized or deserialized as needed, even if only a single field somewhere in the tree is referenced.  The size and depth of an AA is a major factor in this cost, small AA’s are essentially fine, but large and/or deep arrays have a definite cost.  Finally any type of sub/function in an AA gets stripped during this transitions, so these portions can get repeatedly regenerated, even after initialization.

Nodes with fields are reference types, and while they can invoke a rendezvous when access across a boundary, they still work faster for field access as the field itself can be addressed without processing the entire array.  Subs/functions in nodes are also not lost and regenerated as happens in AA’s.

If multiple node fields are going to be referenced in a rendezvous situation, hoisting the node using GetFields() can improve performance by dropping the fields to a single AA, but again keep in mind scale, large AA’s are very expensive.  Also the result of using GetFields() is not a two-way binding to the node, you can change the hoisted AA, but it will not be saved to the node fields itself.  So this is a judgement call based on the cost of multiple rendezvous vs the cost of generating the GetFields() AA and field access.

Finally, if you are going to repeatedly read a given AA or node field in a very large loop, you will increase performance by hoisting that variable prior to the loop. We’re not talking huge leaps here by any stretch, and this would be worthless on a small loop, but on a huge loop even a small performance increase can make the channel feel a bit less sluggish to the users.
