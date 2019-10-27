pugixml for Neverwinter Nights 2
================================

NWN2 uses XML files to describe the UI. These XML files are not W3C
conformant in the following ways:

* Some XML prologs end with `>` instead of `?>`.

```xml
<?xml version="1.0" encoding="utf-8">
```

* Some XML prologs use the not valid encoding `NWN2UI`.

```xml
<?xml version="1.0" encoding="NWN2UI">
```

* Some files contain non UTF-8 characters even if the encoding is UTF-8.
* Some comments contain `--`.
* Documents don't have a single root element.
* There are closing tags without the corresponding opening tag.
* Some attributes are not enclosed in quotes.

```xml
<UIScene name="SCREEN_LEVELUP_BONUS_FEATS" width=1024 height=768 priority="SCENE_INGAME_FULLSCREEN" fullscreen=true cullrenderables=true />
```

* Some attribute values contain characters that should be escaped, e.g.
  quotes.

```xml
<UIScene name="SCREEN_MAINMENU" OnAdd=UIScene_OnAdd_SetupMainMenu("a") />
```

* Sometimes there is no space between an end quote and the following attribute
  name.

```xml
<UIText name="Character"fontfamily="NWN2_Dialog" />
```

For performance reasons, **pugixml** already accepts XML files that contains
some of these incompatibilities. **pugixml for NWN2** is a modified version of
**pugixml** that deals with the rest of incompatibilities.

pugixml
=======

pugixml is a C++ XML processing library, which consists of a DOM-like interface with rich traversal/modification
capabilities, an extremely fast XML parser which constructs the DOM tree from an XML file/buffer, and an XPath 1.0
implementation for complex data-driven tree queries. Full Unicode support is also available, with Unicode interface
variants and conversions between different Unicode encodings (which happen automatically during parsing/saving).

pugixml is used by a lot of projects, both open-source and proprietary, for performance and easy-to-use interface.

## Documentation

Documentation for the current release of pugixml is available on-line as two separate documents:

* [Quick-start guide](https://pugixml.org/docs/quickstart.html), that aims to provide enough information to start using the library;
* [Complete reference manual](https://pugixml.org/docs/manual.html), that describes all features of the library in detail.

You’re advised to start with the quick-start guide; however, many important library features are either not described in it at all or only mentioned briefly; if you require more information you should read the complete manual.

## Example

Here's an example of how code using pugixml looks; it opens an XML file, goes over all Tool nodes and prints tools that have a Timeout attribute greater than 0:

```c++
#include "pugixml.hpp"
#include <iostream>

int main()
{
    pugi::xml_document doc;
    pugi::xml_parse_result result = doc.load_file("xgconsole.xml");
    if (!result)
        return -1;
        
    for (pugi::xml_node tool: doc.child("Profile").child("Tools").children("Tool"))
    {
        int timeout = tool.attribute("Timeout").as_int();
        
        if (timeout > 0)
            std::cout << "Tool " << tool.attribute("Filename").value() << " has timeout " << timeout << "\n";
    }
}
```

And the same example using XPath:

```c++
#include "pugixml.hpp"
#include <iostream>

int main()
{
    pugi::xml_document doc;
    pugi::xml_parse_result result = doc.load_file("xgconsole.xml");
    if (!result)
        return -1;
        
    pugi::xpath_node_set tools_with_timeout = doc.select_nodes("/Profile/Tools/Tool[@Timeout > 0]");
    
    for (pugi::xpath_node node: tools_with_timeout)
    {
        pugi::xml_node tool = node.node();
        std::cout << "Tool " << tool.attribute("Filename").value() <<
            " has timeout " << tool.attribute("Timeout").as_int() << "\n";
    }
}
```


## License

This library is available to anybody free of charge, under the terms of MIT License (see LICENSE.md).
