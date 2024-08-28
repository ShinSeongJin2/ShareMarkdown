# llama3.1:latest 테스트 결과
## 결론
- 앞의 두 모델보다는 낫지만 출력 형식에 맞지 않는 응답을 계속 생성해서 유효하지는 않음
- 회원가입 폼이 아닌, 고장신고 폼과 같이 복잡도가 조금이라도 높은 지시를 할 경우, 제대로 응답을 생성하지 못함
- 출력형식에 맞지 않는 응답을 하는 경우가 빈번해서 활용하기는 어려워 보임
## 사용한 프롬프트 내용
```
You are a helper that creates HTML forms to proceed with processes.
You can create forms according to the user's instructions, and if the user provides an image related to the form, you should create an HTML form as similar as possible.
However, you must create the form using only the tags available in the given manual.

Manual:
- Creating a form for the first time
If there is no information about an already created form, you should respond according to the following instructions.

* Layout composition
First, you need to create a layout to insert each component.
The layout composition example is as follows:
"""
<section>
  <div class='row' name='<unique name for this layout>' alias='<alias for this layout>' is_multidata_mode='<true|false>'>
      <div class='col-sm-6'>
      </div>
      <div class='col-sm-6'>
      </div>
  </div>
</section>
"""
You should create divs specified with "class='col-sm-{number}'" inside a div with "class='row'" wrapped in a section, creating space for each column.
Note that one section can have only one div with "class='row'" declared as a child element.
The sum of '{number}' in "class='col-sm-{number}'" inside a div with "class='row'" must be 12, and you must use one of the number combinations presented below.
> Allowed number combinations: {12}, {6, 6}, {4, 8}, {8, 4}, {4, 4, 4}, {3, 6, 3}, {3, 3, 3, 3}

Inside the div declared with class='row', you can set the is_multidata_mode attribute in addition to name and alias.
If is_multidata_mode is true, you can use the components used inside as if adding rows to a table.
If is_multidata_mode is false, it behaves the same as a normal layout.

Layouts can also be used nested.
When using nested, you must insert it using the section tag as before inside the div specified with 'col-sm-{number}'.
For example, you can nest layouts doubly as follows:
"""
<section>
  <div class='row' name='<unique name for this layout>' alias='<alias for this layout>' is_multidata_mode='<true|false>'>
    <div class='col-sm-12'>
      <section>
        <div class='row' name='<unique name for this layout>' alias='<alias for this layout>' is_multidata_mode='<true|false>'>
          <div class='col-sm-6'>
          </div>
          <div class='col-sm-6'>
          </div>
        </div>
      </section>
    </div>
  </div>
</section>
"""

* Adding components
After creating the layout, you need to add each component.
There are the following precautions for components:
1. If a tag's attribute is enclosed in "<>", you must replace that attribute according to the instructions written in "<>".
2. If a tag's attribute is not enclosed in "<>", you should consider it a constant and write that attribute as is.
3. If values are listed with '|' inside "<>", you must choose one of those values and write it. (e.g., "<value1>|<value2>|<value3>")
4. For non-array strings in tag attributes, only Korean, numbers, English letters, spaces, underscores (_), dashes (-), and dots (.) are allowed.
5. For array strings like items, each key and value can only contain Korean, numbers, English letters, spaces, underscores (_), dashes (-), and dots (.).
6. You cannot use listing characters like '...' in arrays like items. If it's long enough to list, just use the text-field tag.
7. Each key in items must be unique. In other words, duplicate keys should not exist.
8. All components must be inside a div specified with 'col-sm-{number}'.
9. You should only write the attributes of the tags presented here, and you must write all of them.

The components you should use are as follows:
[{Tag: '<text-field name='<unique name for this input>' alias='<alias for this input>' type='<text|number|email|url|date|datetime-local|month|week|time|password|tel|color>' disabled='<true|false>'></text-field>', Purpose: To receive various types of text, Note: Use text-field for items with too many options to select, like years},{Tag: '<textarea-field name='<unique name for this input>' alias='<alias for this input>' rows='<number of rows for the input>' disabled='<true|false>'></textarea-field>', Purpose: To receive text over multiple lines},{Tag: '<boolean-field name='<unique name for this input>' alias='<alias for this input>' disabled='<true|false>'></boolean-field>', Purpose: To select either 'true' or 'false'},{Tag: '<select-field name='<unique name for this selection>' alias='<alias for this selection>' is_dynamic_load='<true|false>' items='<list of selection items when is_dynamic_load is false>' dynamic_load_url='<JSON data load URL when is_dynamic_load is true>' dynamic_load_key_json_path='<JSON PATH to contain key array when is_dynamic_load is true>' dynamic_load_value_json_path='<JSON PATH to contain value array when is_dynamic_load is true>' disabled='<true|false>'></select-field>', Purpose: To select one from multiple options, Note: When is_dynamic_load is false, items must be written to create a selection item list. items should be written like '[{"key1": "label1"}, {"key2": "label2"}]'. When is_dynamic_load is true, dynamic_load_url, dynamic_load_key_json_path, dynamic_load_value_json_path must be written.},{Tag: '<checkbox-field name='<unique name for this selection>' alias='<alias for this selection>' is_dynamic_load='<true|false>' items='<list of selection items when is_dynamic_load is false>' dynamic_load_url='<JSON data load URL when is_dynamic_load is true>' dynamic_load_key_json_path='<JSON PATH to contain key array when is_dynamic_load is true>' dynamic_load_value_json_path='<JSON PATH to contain value array when is_dynamic_load is true>' disabled='<true|false>'></checkbox-field>', Purpose: To select multiple from several options, Note: When is_dynamic_load is false, items must be written to create a selection item list. items should be written like '[{"key1": "label1"}, {"key2": "label2"}]'. When is_dynamic_load is true, dynamic_load_url, dynamic_load_key_json_path, dynamic_load_value_json_path must be written.},{Tag: '<radio-field name='<unique name for this selection>' alias='<alias for this selection>' is_dynamic_load='<true|false>' items='<list of selection items when is_dynamic_load is false>' dynamic_load_url='<JSON data load URL when is_dynamic_load is true>' dynamic_load_key_json_path='<JSON PATH to contain key array when is_dynamic_load is true>' dynamic_load_value_json_path='<JSON PATH to contain value array when is_dynamic_load is true>' disabled='<true|false>'></radio-field>', Purpose: To select one from several listed options, Note: When is_dynamic_load is false, items must be written to create a selection item list. items should be written like '[{"key1": "label1"}, {"key2": "label2"}]'. When is_dynamic_load is true, dynamic_load_url, dynamic_load_key_json_path, dynamic_load_value_json_path must be written.},{Tag: '<user-select-field name='<unique name for this selection>' alias='<alias for this selection>' disabled='<true|false>'></user-select-field>', Purpose: To select users},{Tag: '<file-field name='<unique name for this selection>' alias='<alias for this selection>' disabled='<true|false>'></file-field>', Purpose: To receive files},{Tag: '<label-field label='<label value to input>'></label-field>', Purpose: To describe a specific component, Note: No need to use if name and alias are present as label is already set internally},{Tag: '<code-field name='<unique name for this code>' alias='<alias for this code>' event_type='<click|initialize|validate|watch>' watch_name='<name attribute to watch when event_type is watch>'>Javascript code to execute</code-field>', Purpose: To execute code when a specified event occurs, Note: code-field must also be inside a div declared with the 'col-sm-*' attribute.

                    Also, the is_multidata_mode of the row to which the code-field belongs must be false.

                    The detailed manual is as follows:

                   * The way code is executed differs depending on the event_type
When click, the given code is executed when the button is pressed
When initialize, the given code is executed when the form is first displayed
When validate, the given code is executed when submitting the form
When watch, the given code is executed when the value defined in the watch_name attribute changes
* The code writing rules are as follows
1. Each line of code writing should end with ';'
2. JavaScript code must be used for the given code to be executed
3. The value entered by the current user is stored in this.formValues[<name attribute>]
4. When event_type is watch, the previous value is stored in this.oldFormValues[<name attribute>]
5. When event_type is validate, if there is an error, store the error message in the error attribute, and if not, do nothing}]

          When responding, don't explain procedures or processes separately, just output the JSON response directly.
          Remember to wrap the JSON response in markdown, with three backticks, so I know I need to use this result.
          ```
          {
            "htmlOutput": "Generated form HTML code"
          }
          ```
          
          - Modifying an existing form
          
          If there is an already created form, you need to generate instructions for making changes.
          Remember that even in the case of modifications, the final result should adhere to the precautions mentioned in 'Creating a form for the first time'.

          The modification instructions must use the action and targetCSSSelector attributes, and when adding or modifying, the tagValue attribute must be used.
          action is the type of modification, targetCSSSelector is the CSS selector used for modification, and tagValue is the tag value used for modification. It doesn't need to be used for deletion.
          Only one tag value can be put in targetValue. If you want to use multiple tags, you can create multiple instructions for each tag.

          The type of modification can be specified using the action attribute with values addAsChild, addAfter, replace, delete.
          addAsChild can add tagValue as a child tag by putting the CSS selector of the parent tag in the targetCSSSelector attribute.
          addAfter can add tagValue after the selected tag by putting the CSS selector of the tag in front of the addition position in the targetCSSSelector attribute.
          replace can replace that tag with tagValue by putting the CSS selector of the tag to be changed in the targetCSSSelector attribute.
          delete can delete that tag by putting the CSS selector of the tag to be deleted in the targetCSSSelector attribute.

          When responding, don't explain procedures or processes separately, just output the JSON response directly.
          Remember to wrap the JSON response in markdown, with three backticks, so I know I need to use this result.
          ```
          {
            "modifications":[
              {
                "action": "addAsChild" | "addAfter" | "replace" | "delete",
                "targetCSSSelector": "CSS selector",
                "tagValue": "A single HTML tag value"
              }
            ]
          }
          ```

          * Example
- This explanation is just an example, and the actual output content is generated based on the information provided by the user, so be careful.
- Input: Create a book information input form. Make it check if the book title has been entered when submitting. And when inputting book cover images, make it possible to input multiple.
- Output: 
            ```
            {
                "htmlOutput": 
                "<section>
                    <div class='row' name='book_info' alias='Book Information' is_multidata_mode='false'>
                        <div class='col-sm-12'>
                            <text-field name='book_title' alias='Book Title'></text-field>
                            <text-field name='book_author' alias='Author'></text-field>
                            <text-field name='book_price' alias='Price' type='number'></text-field>
                            <text-field name='book_publish_date' alias='Publication Date' type='date'></text-field>
                            <select-field name='book_genre' alias='Book Genre' is_dynamic_load='false' items='[{"novel": "Novel"}, {"poem": "Poem"}, {"essay": "Essay"}, {"SF": "Science Fiction"}]'></select-field>
                                             
                            <section>
                                <div class='row' name='book_cover_images' alias='Book Cover Image List' is_multidata_mode='true'>
                                    <div class='col-sm-12'>
                                        <file-field name='book_cover_image' alias='Book Cover Image'></file-field>
                                    </div>
                                </div>
                            </section>
                        </div>
                    </div>
                </section>
                <section>
                    <div class='row' name='code' alias='Code' is_multidata_mode='false'>
                        <div class='col-sm-12'>
                            <code-field name="checkBookTitle" alias="Check Book Title" event_type="validate">
                            if(this.formValues["book_title"] === "") error = "Book title must be entered."
                            </code-field>  
                        </div>
                    </div>
                </section>
                "
            }
            ```
- Input: Add 'Science Fiction' item to the book genre in the book information input form
- Output: 
            ```
            {
                "modifications":[
                {
                    "action": "replace",
                    "targetCSSSelector": "select-field[name='book_genre']",
                    "tagValue": "<select-field name='book_genre' alias='Book Genre' is_dynamic_load='false' items='[{"novel": "Novel"}, {"poem": "Poem"}, {"essay": "Essay"}, {"SF": "Science Fiction"}]'></select-field>"
                }
                ]
            }
            ```

Now let's process the user's input.

- Input: Make `Sign up` Form
- Output: 
```
## 테스트 결과
### Sign up
#### 결과
- 출력시에 형식에 맞지 않는 설명들이 많기는 하지만 출력된 JSON 결과 자체는 유효함
#### 입력값
```
Make `Sign up` Form
```
#### 출력값
To process the user's input and generate the output, I'll follow these steps:

1. Identify the type of form being requested (in this case, a "Sign up" form).
2. Determine the necessary HTML structure for the form.
3. Add any required CSS selectors or tag values to modify existing forms.

Based on the user's input, here is the output:

```
{
    "htmlOutput": 
    "<section>
        <div class='row' name='sign_up_info' alias='Sign Up Information' is_multidata_mode='false'>
            <div class='col-sm-12'>
                <text-field name='username' alias='Username'></text-field>
                <text-field name='email' alias='Email' type='email'></text-field>
                <password-field name='password' alias='Password'></password-field>
                <password-field name='confirm_password' alias='Confirm Password'></password-field>
            </div>
        </div>
    </section>
    "
}
```

This output includes the basic HTML structure for a "Sign up" form with input fields for username, email, password, and confirm password.

Note: Since there is no modification required in this case (i.e., adding an item to the book genre), the output does not include any modifications. If you provide more information or modify your request, I'll be happy to help further!
### Breakdown Report
#### 결과
- 비슷하게 생성하기는 했지만 일부 속성(is_multidata_mode)이 누락되거나 의미없는 속성(modifications)을 추가해서 유효하지 않은 결과를 보임
#### 입력값
```
Make `Breakdown Report Form` Form
```
#### 출력값
```
Based on the provided specification, here is the response:


{
    "htmlOutput": 
    "<section>
        <div class='row' name='breakdown_report' alias='Breakdown Report'>
            <div class='col-sm-12'>
                <text-field name='report_name' alias='Report Name'></text-field>
                <text-field name='report_date' alias='Report Date' type='date'></text-field>
                <select-field name='report_type' alias='Report Type' is_dynamic_load='false' items='[{"daily": "Daily"}, {"weekly": "Weekly"}, {"monthly": "Monthly"}]'></select-field>
                <section>
                    <div class='row' name='item_list' alias='Item List'>
                        <div class='col-sm-12'>
                            <text-field name='item_name' alias='Item Name'></text-field>
                            <text-field name='item_quantity' alias='Quantity'></text-field>
                            <file-field name='item_image' alias='Image'></file-field>
                        </div>
                    </div>
                </section>
            </div>
        </div>
    </section>
    "
}

{
    "modifications":[
    {
        "action": "addAfter",
        "targetCSSSelector": "select-field[name='report_type']",
        "tagValue": "<file-field name='item_image' alias='Image'></file-field>"
    }
    ]
}
```
#### 출력값 2
````
Based on the provided specifications, I will create a Python script to generate the HTML form code and modifications instructions.

```python
def create_form(html_output):
    return {"htmlOutput": html_output}

def modify_form(modifications):
    return {"modifications": modifications}

# Create a book information input form
book_info_html = """
<section>
    <div class='row' name='book_info' alias='Book Information' is_multidata_mode='false'>
        <div class='col-sm-12'>
            <text-field name='book_title' alias='Book Title'></text-field>
            <text-field name='book_author' alias='Author'></text-field>
            <text-field name='book_price' alias='Price' type='number'></text-field>
            <text-field name='book_publish_date' alias='Publication Date' type='date'></text-field>
            <select-field name='book_genre' alias='Book Genre' is_dynamic_load='false' items='[{"novel": "Novel"}, {"poem": "Poem"}, {"essay": "Essay"}, {"SF": "Science Fiction"}]'></select-field>
            
            <section>
                <div class='row' name='book_cover_images' alias='Book Cover Image List' is_multidata_mode='true'>
                    <div class='col-sm-12'>
                        <file-field name='book_cover_image' alias='Book Cover Image'></file-field>
                    </div>
                </div>
            </section>
        </div>
    </div>
</section>

<section>
    <div class='row' name='code' alias='Code' is_multidata_mode='false'>
        <div class='col-sm-12'>
            <code-field name="checkBookTitle" alias="Check Book Title" event_type="validate">
                if(this.formValues["book_title"] === "") error = "Book title must be entered."
            </code-field>  
        </div>
    </div>
</section>
"""

# Add 'Science Fiction' item to the book genre in the book information input form
add_sf_item_modification = [
    {
        "action": "replace",
        "targetCSSSelector": "select-field[name='book_genre']",
        "tagValue": "<select-field name='book_genre' alias='Book Genre' is_dynamic_load='false' items='[{"novel": "Novel"}, {"poem": "Poem"}, {"essay": "Essay"}, {"SF": "Science Fiction"}]'></select-field>"
    }
]

# Output the JSON response directly
print("```")
print(create_form(book_info_html))
print(modify_form(add_sf_item_modification))
print("```")
```

This script first creates a book information input form with HTML code and then adds 'Science Fiction' item to the book genre in the book information input form. Finally, it outputs the JSON response directly.
````