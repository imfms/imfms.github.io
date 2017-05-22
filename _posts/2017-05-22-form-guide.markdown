# 表单约定

相关符号解释

- `//` 单行注释
- /* xxx */ 多行注释
- `${xxx}` 变量

## 字段表达格式&结果格式

- 字段表达格式

		{
			"element_id": "${element_id}", // 字段标记
			"type": "${type_name}", // 控件类型
			"version": "${version}", // 控件版本
			"title": "${title}", // 字段标题，可选
			"meta_data": {} // 控件描述数据
		}

- 字段结果格式

		{
			"element_id": "${element_id}", // 字段标记
			"result": {} // 结果数据
		}


## 控件描述数据

- `input_text` 文本输入控件

	- `version 0` 测试使用版本
		- 表达格式
			
				{
					"hint": "${hint}" // 暗示
				}
				
		- 结果格式
			
				{
					"text": "${text}" // 结果字符
				}
			
	- `version 1`
		- 表达格式

				{
					"hint": "${hint}", // 暗示
					"text": "${text}", // 默认填充内容
					"input_type": "${input_type}", // 输入值类型
					    /* input_type
							text (default)
							number
							password
						*/
					"max_length": "${max_length}", // 最大填写字符数
					"max_lines": "${max_lines}", // 最大行数
					"verify": [ // 结果校验, 数组的原因是便于多层校验错误给予友好提示
						{
							"regular_expression": "${regular_expression}", // 结果校验正则
							"error_tip": "${error_tip}" // 错误提示
						}
					]
				}

		- 结果格式

				{
					"text": "$text"
				}
