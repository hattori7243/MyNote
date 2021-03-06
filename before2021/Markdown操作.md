# Markdown学习
   md后缀的文件就是Markdown的文件，安转vim后，使用`vim XX.md`命令打开文件。

1. **基础语句**
    
   1. **标题**
          
      写标题时可在题目前加`#`，最多可加6个，几个`#`就代表第几级标题，如下所示
      ```bash
      # 1级标题
      ## 2级标题
      ###### 6级标题
      ####### 7个#就没意义了
      ```
      效果：
      # 1级标题
      ## 2级标题
      ###### 6级标题
      ####### 7个`#`就没意义了
          
   2. **文本样式**
          
      针对文字加粗、斜体、划线、混合等模式见下表，如果不想被转义，可以在符号前加\
      
      样式 | 语句 | 示例 | 效果
      ------------ | ------------- | ------------ | ------------- 
      加粗 | `** **` or `__ __` | `**Bold**` or `__Bold__` | **Bold** or __Bold__
      斜体 | `* *` or `_ _` | `*Italic*` or `_Italic_` | *Italic* or _Italic_
      划线 | `~~ ~~` | `~~Strikethrough~~` | ~~Strikethrough~~ 
      混合 | `** **` and `__ __` | `**Bold and _Italic_**` | **Bold and _Italic_**
          
   3. **引用**
          
      - 引用文本时可在前面加`>`  
	示例如下：
	    ```bash
       	> 引用 
	    ```        
        效果：
	    > 引用             

      - 引用代码时可使用单个反引号
      
        示例如下：
            ```
            文本中有句`代码`
            ```
              
	    效果：
            文本中有句`代码`
          
      - 引用大段代码时则需要三个反引号
      
        示例如下：    
	    ```bash  
         	```
			code1
			code2
			code3
			```
	    ```
            
         效果：
	     ```
	     code1
	     code2
	     code3
	     ```

   4. **链接**
   
      针对图片或文本进行链接
      - 对需要链接的文本使用[],链接的URL使用()`
        
         示例如右：`[Baidu](www.baidu.com)` 
	 
	     效果：[Baidu](https://baidu.com/)              
          
      - 对需要链接的图片使用![]，链接的URL使用()
            
         示例如下：
         ```
         ![Image of Yaktocat](https://octodex.github.com/images/yaktocat.png)
         ```
         效果：
         ![Image of Yaktocat](https://octodex.github.com/images/yaktocat.png)
          
   5. **列表**
   
      针对文本内容进行列表排序图片或文本进行链接
    
      - 针对无序列表，可以在每个列表项前加星号`*`或横线`-`
          
        示例如下:
        ```
	    * 项目1
	    * 项目2
	    - 项目1
	    - 项目2
	    ```
	     
	     效果如下：
	     * 项目1
	     * 项目2
	    
	     - 项目1
	     - 项目2
          
      - 针对有序列表，可以在每个列表项前加数字及英文句号
          
        示例如下:
	     ```
	     1. 项目1
	     2. 项目2
	     3. 项目1
	     4. 项目2
	     ```
	     
	    效果如下：
	     1. 项目1
	     2. 项目2
	     3. 项目3
	     4. 项目4

      - 针对嵌套列表，可以在子项前空2格
          
        示例如下:
        ```
	    1. file1
	       - file1-1
      	       - file1-2
	    2. file2
	       1. file2-1
		       2. file2-2
	       ```
	     
	    效果如下：
	     1. file1
	        - file1-1
		    - file1-2
	     2. file2
	        1. file2-1
		    2. file2-2