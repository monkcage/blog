---
layout: post
title: XML操作-pugixml
category: xml
tags: [c++, xml]
---

    test.xml
    <?xml version="1.0" encoding="utf-8">
    <root>
        <name>test</name>
        <info>
             <age>18</age>
             <sex>F</sex>
        </info>
    </root>



## 读操作
    
    pugi::xml_document     doc;
    pugi::xml_parse_result result = doc.load_file("test.xml");
    pugi::xml_node         node_root = doc.child("root");
    pugi::xml_node         node_name = node_root.child("name");
    char_t                 *name_text = node_name.text().as_string();
    
    for(pugi::xml_node node = node_root.child("info").first_child();
        node; node = node.next_sibling()){
           std::count << node.text() << std::endl;
        }
        
    pugi::xml_node        node_info = node_root.child("info");
    pugi::xml_node        node_age = node_info.child("age");
    int                   age = node_age.text().as_int();
        
        
## 写操作
   
    pugi::xml_document doc;
    pugi::xml_node     node_root = doc.append_child("root");
   
    // declare
    pugi::xml_node     node_pre = doc.prepend_child(pugi::node_declaration);
    node_pre.append_attribute("version") = "1.0";
    node_pre.append_attribute("encoding") = "utf-8";
   
    // comment
    pugi::xml_node     comment = node_root.append_child(pugi::node_comment);
    comment.set_value("Just for test!");
   
    pugi::xml_node    node_name = node_root.append_child("name").text().set("test");
    pugi::xml_node    node_info = node_root.append_child("info")
    pugi::xml_node    node_age = node_info.append_child("age").text().set(18);
    pugi::xml_node    node_sex = node_info.append_child("sex").text().set("F");
   
    doc.save_file(filename, "\t", 1U, pugi::encoding_utf8);
    
## 删除
    
    node.remove_child(node_name)
