# 表单控件约定

相关符号解释

- `//` 单行注释
- /* xxx */ 多行注释
- `${xxx}` 变量

## 控件表达规范&结果规范

- 控件表达规范

		{
			"element_id": "${element_id}", // 字段标记id
			"type": "${type_name}", // 控件类型
			"version": "${version}", // 控件版本
			"title": "${title}", // 控件标题
			"show_desc_data": "${show_desc_data}", // 控件展示描述数据
			"result_desc_data": "${result_desc_data}" // 控件展示结果描述数据，可选，用于填充结果到当前控件(例如：修改表单时)
		}

- 控件结果规范

		{
			"element_id": "${element_id}", // 字段标记
			"result": "${result}" // 结果描述数据
		}


## 控件描述数据

### `input_text` 文本输入控件

#### `version 0`

- 展示描述数据格式

		{
			"hint": "${hint}", // 编辑框暗示，可选，默认无
			"text": "${text}", // 默认填充内容，可选，默认无
			"input_type": "${input_type}", // 输入值类型，可选，默认text
			    /* input_type
					text // 普通输入文本，默认
					number // 整数(不可为负)
						校验正则: ^\d+$
					number_decimal // 小数(不可为负)
						校验正则: ^\d+\.{0,1}\d{0,}$
					number_signed // 整数(可包含正负)
						校验正则: ^-{0,1}\d+$
					text_password // 文本密码输入类型
					number_password // 数字密码输入类型
						校验正则: ^\d+$
				*/
			"max_length": "${max_length}", // 最大填写字符数，可选，默认无限制
			"max_lines": "${max_lines}", // 最大行数，可选，默认无限制
				/*
					校验正则
						(.{0,}\n.{0,}){max_lines,}
					max_lines = 最大行数
				*/
			"verify": [ // 结果校验, 数组的原因是便于多重校验错误时给予用户友好提示，可选，默认无
				{
					"regular_expression": "${regular_expression}", // 正则表达式校验规则
					"error_tip": "${error_tip}" // 校验失败提示
				}
			]
		}

- 结果描述数据格式

		"${result}"

### `date_select` 日期选择控件

#### `version 0`
- 展示描述数据格式
		
		{
			"is_fill_local_time": "${is_fill_local_time}", // 是否填充当前时间，可选，默认否
				/* is_fill_local_time
					true // 填充
					false // 不填充，默认
				*/
			"more_than_check": [ // 选择时间必须超过的时间戳规则集，可选，默认无限制
				{
					"timestamp": "${timestamp}", // 选择时间必须大于等于的时间戳
					"error_tips": "${error_tips}" // more_than_timestamp校验失败弹出提示内容，可选，默认提示 时间选择不能超过'more_than_timestamp代表的日期'
				}
			],
			"less_than_check": [ // 选择时间必须小于等于的时间戳规则集，可选，默认无限制
				{
					"timestamp": "${timestamp}", // 选择时间必须小于的时间戳
					"error_tips": "${error_tips}" // less_than校验失败弹出提示内容，可选，默认提示 时间选择不能小于'less_than_timestamp代表的日期'
				}
			]
		}

- 结果描述数据格式

		"${result}" // 时间选择结果时间戳, 未选择为 -1

### `single_choice` 单选框

#### `version 0`

- 展示描述数据格式

		{
			"options": [ // 单选框包含的所有元素
				{
					"choice_id": "${choice_id}", // 选项id
					"choice_title": "${choice_title}" // 选项标题
				}
			],
			"default_select": "${default_select_choice_id}" // 默认选择id(该id必须在options节点下存在)，可选，默认不选
		}

- 结果描述数据格式

		"${select_choice_id}" // 最终选择的选项id

### `multi_choice` 多选框

#### `version 0`

- 展示描述数据格式

		{

			"options": [ // 多选框包含的所有元素
				{
					"choice_id": "${choice_id}", // 选项id
					"choice_title": "${choice_title}" // 选项标题
				}
			],
			"default_select": [ // 默认选择id集(该id必须在options节点下存在)，可选，默认不选
				"${default_select_id1}" // choice_id
			],
			"min_select_num": "${min_select_num}", // 最少选择数量，可选，默认不限制
			"max_select_num": "${max_select_num}" // 最大选择数量，可选，默认为所有选项数量
		}

- 结果描述数据格式

		[ // 最终选择的选项id集
			"${select_choice_id}" // 某个选项id
		]
