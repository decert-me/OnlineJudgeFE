<template>
	<div class="problem">
		<Panel :title="title">
			<el-form ref="form" :model="problem" :rules="rules" label-position="top" label-width="70px">
				<el-row :gutter="20">

					<el-col :span="18">
						<el-form-item prop="title" :label="$t('m.Title')">
							<el-input :placeholder="$t('m.Title')" v-model="programmingQuestions.title" required></el-input>
						</el-form-item>
					</el-col>
				</el-row>
				<el-row :gutter="20">
					<el-col :span="24">
						<el-form-item prop="description" label="描述">
<!-- 
                             <simditor :options="options" id="1" @change="change" style="width: 100%">
    </simditor> -->
							<!-- <textarea id="editor" v-model="programmingQuestions.description"></textarea> -->
							<simditor
								id="test1"
								:options="options"
								@change="change">
							</simditor>

						</el-form-item>
					</el-col>
				</el-row>

				<el-row :gutter="20">
					<el-col :span="20">
						<el-form-item :label="$t('m.Languages')" :error="error.languages">
							<el-checkbox-group v-model="programmingQuestions.languages">
								<el-tooltip class="spj-radio" v-for="lang in languages" :key="'spj'+lang" effect="dark" :content="lang.description" placement="top-start">
									<el-checkbox :label="lang.name"></el-checkbox>
								</el-tooltip>
							</el-checkbox-group>
						</el-form-item>
					</el-col>
				</el-row>
				<div>
					<el-form-item v-for="(input, index) in programmingQuestions.input" :key="'sample'+index">
						<Accordion :title="'Sample' + (index + 1)">
							<el-button type="warning" size="small" icon="el-icon-delete" slot="header" @click="deleteSample(index)">
								Delete
							</el-button>
							<el-row :gutter="20">

								<el-col :span="12">
									<el-form-item :label="$t('m.Input_Samples')" required>
										<el-input :rows="5" type="textarea" :placeholder="$t('m.Input_Samples')" v-model="programmingQuestions.input[index]">
										</el-input>
									</el-form-item>
								</el-col>

								<el-col :span="12">
									<el-form-item :label="$t('m.Output_Samples')" required>
										<el-input :rows="5" type="textarea" :placeholder="$t('m.Output_Samples')" v-model="programmingQuestions.output[index]">
										</el-input>
									</el-form-item>
								</el-col>

							</el-row>
						</Accordion>
					</el-form-item>
				</div>
				<!-- 添加样例 -->
				<div class="add-sample-btn">
					<button type="button" class="add-samples" @click="addSample()"><i class="el-icon-plus"></i>{{$t('m.Add_Sample')}}
					</button>
				</div>
				<!-- 代码片段位置 -->
				<el-form-item :label="$t('m.Code_Template')">
					<el-row>
						<el-col :span="24" v-for="(v, k) in languages1" :key="'template'+k">
							<el-form-item>
								<el-checkbox v-model="v.open">{{ v.name }}</el-checkbox>
								<div v-if="v.open">
									<code-mirror v-model="v.value"></code-mirror>
								</div>
							</el-form-item>
						</el-col>
					</el-row>
				</el-form-item>

				<el-form-item label="类型" :error="error.spj">
					<el-col :span="24" >
						<el-radio v-model="programmingQuestions.type" label="coding"  @input="isSpecialJudgeCoding=false">coding</el-radio>
						<el-radio v-model="programmingQuestions.type" label="special_judge_coding" @input="isSpecialJudgeCoding=true">special_judge_coding</el-radio>
					</el-col>
				</el-form-item>

				<!-- 类型4的时候打开 -->
				<el-form-item v-if="isSpecialJudgeCoding">
					<Accordion :title="$t('m.Special_Judge_Code')">
						<template slot="header">
							<span>{{$t('m.SPJ_language')}}</span>
							<el-radio-group v-model="problem.spj_language">
                            <span  class="spj-radio" v-for="lang in languages2" :key="lang">
                            	<el-radio :label="lang">{{ lang }}</el-radio></span>
							</el-radio-group>
							<el-button type="primary" size="small" icon="el-icon-fa-random" @click="compileSPJ" :loading="loadingCompile">
								{{$t('m.Compile')}}
							</el-button>
						</template>
						<code-mirror v-model="programmingQuestions.spj_code" :mode="spjMode"></code-mirror>
					</Accordion>
				</el-form-item>

				<el-form-item :label="$t('m.Source')">
					<el-input :placeholder="$t('m.Source')" v-model="programmingQuestions.score" @input="ifNumber"></el-input>
				</el-form-item>
				<el-button @click.native="submit()">Save</el-button>
				<el-button class="copy" @click="copy(jsonData)">Copy JSON</el-button>
			</el-form>

			<div class="json">
                <el-input type="textarea" :rows="2" v-model="jsonData"></el-input>
            </div>
		</Panel>
	</div>
</template>

<script>
// import Simditor from '../../components/Simditor'
import Accordion from '../../components/Accordion'
import CodeMirror from '../../components/CodeMirror'
import api from '../../api'
import Clipboard from 'clipboard'
// import Simditor from "simditor";
import Simditor from '../../components/Simditor'

export default {
	name: 'Problem',
	components: {
		Simditor,
		Accordion,
		CodeMirror,
	},
	data() {
		return {
              content: "",
      //工具栏配置项
      options: {
        placeHolder: "this is placeHolder",
        toolbarFloat: false,
        toolbar: [
          "bold",
          "italic",
          "title",
          "link",
          "image",
          "ol",
          "ul",
          "indent",
          "outdent",
          "alignment",
          // "underline",
          // "strikethrough",
          // "fontScale",
          "color",
          // "|",
          // "blockquote",
          // "code",
          // "table",
          // "|",
          // "hr",
          // "|",
        ],
        pasteImage: true, //占位符(图片)
        upload: {
          url: `http://...`, //文件上传的接口地址
          params: null, //键值对,指定文件上传接口的额外参数,上传的时候随文件一起提交
          fileKey: "file", //服务器端获取文件数据的参数名
          connectionCount: 3, //同时上传多少张图片
          leaveConfirm: "正在上传文件",
        },
      },
			jsonData: '',
			isSpecialJudgeCoding: false,
			programmingQuestions: {
				title: '',
				description: '',
				input: [],
				output: [],
				languages: [],
				code_snippets: [],
				type: 'coding',
				score: '',
                spj_code:''
			},
			radio: '',
			languages: [
				{ name: 'C', description: 'GCC 9.4' },
				{ name: 'C++', description: 'G++ 9.4' },
				{ name: 'Java', description: 'OpenJDK 11' },
				{ name: 'Python2', description: 'Python 2.7' },
				{ name: 'Python3', description: 'Python 3.6' },
				{ name: 'Golang', description: 'Golang 1.17' },
				{ name: 'JavaScript', description: 'Node14' },
				{ name: 'Solidity', description: 'Solidity' },
			],
			languages1: [
				{ name: 'C', open: '', value: '' },
				{ name: 'C++', open: '', value: '' },
				{ name: 'Java', open: '', value: '' },
				{ name: 'Python2', open: '', value: '' },
				{ name: 'Python3', open: '', value: '' },
				{ name: 'Golang', open: '', value: '' },
				{ name: 'JavaScript', open: '', value: '' },
				{ name: 'Solidity', open: '', value: '' },
			],

	languages2: [
				'C','C++', 'Solidity'
			],
			rules: {
				_id: {
					required: true,
					message: 'Display ID is required',
					trigger: 'blur',
				},
				title: {
					required: false,
					message: 'Title is required',
					trigger: 'blur',
				},
				input_description: {
					required: true,
					message: 'Input Description is required',
					trigger: 'blur',
				},
				output_description: {
					required: true,
					message: 'Output Description is required',
					trigger: 'blur',
				},
			},
			loadingCompile: false,
			mode: '',
			contest: {},
			problem: {
				languages: [],
				io_mode: {
					io_mode: 'Standard IO',
					input: 'input.txt',
					output: 'output.txt',
				},
			},
			reProblem: {
				languages: [],
				io_mode: {
					io_mode: 'Standard IO',
					input: 'input.txt',
					output: 'output.txt',
				},
			},
			testCaseUploaded: false,
			allLanguage: {
				spj_languages: [{ lang: 'python' }],
			},
			inputVisible: false,
			tagInput: '',
			template: {},
			title: '',
			spjMode: '',
			disableRuleType: false,
			routeName: '',
			error: {
				tags: '',
				spj: '',
				languages: '',
				testCase: '',
			},
		}
	},
	mounted() {
		this.routeName = this.$route.name
		if (
			this.routeName === 'edit-problem' ||
			this.routeName === 'edit-contest-problem'
		) {
			this.mode = 'edit'
		} else {
			this.mode = 'add'
		}
		api.getLanguages().then((res) => {
			this.problem = this.reProblem = {
				_id: '',
				title: '',
				description: '',
				input_description: '',
				output_description: '',
				time_limit: 1000,
				memory_limit: 256,
				difficulty: 'Low',
				visible: true,
				share_submission: false,
				tags: [],
				languages: [],
				template: {},
				samples: [{ input: '', output: '' }],
				spj: false,
				spj_language: '',
				spj_code: '',
				spj_compile_ok: false,
				test_case_id: '',
				test_case_score: [],
				rule_type: 'ACM',
				hint: '',
				source: '',
				io_mode: {
					io_mode: 'Standard IO',
					input: 'input.txt',
					output: 'output.txt',
				},
			}
			let contestID = this.$route.params.contestId
			if (contestID) {
				this.problem.contest_id = this.reProblem.contest_id = contestID
				this.disableRuleType = true
				api.getContest(contestID).then((res) => {
					this.problem.rule_type = this.reProblem.rule_type =
						res.data.data.rule_type
					this.contest = res.data.data
				})
			}

			this.problem.spj_language = 'C'

			let allLanguage = res.data.data
			// this.allLanguage = allLanguage

			// get problem after getting languages list to avoid find undefined value in `watch problem.languages`
			if (this.mode === 'edit') {
				this.title = this.$i18n.t('m.Edit_Problem')
				let funcName = {
					'edit-problem': 'getProblem',
					'edit-contest-problem': 'getContestProblem',
				}[this.routeName]
				api[funcName](this.$route.params.problemId).then(
					(problemRes) => {
						let data = problemRes.data.data
						if (!data.spj_code) {
							data.spj_code = ''
						}
						data.spj_language = data.spj_language || 'C'
						this.problem = data
						this.testCaseUploaded = true
					}
				)
			} else {
				this.title = this.$i18n.t('m.Add_Problem')
				for (let item of allLanguage.languages) {
					this.problem.languages.push(item.name)
				}
			}
		})
	},
	watch: {
		$route() {
			this.$refs.form.resetFields()
			this.problem = this.reProblem
		},
		'problem.languages'(newVal) {
			let data = {}
			// use deep copy to avoid infinite loop
			let languages = JSON.parse(JSON.stringify(newVal)).sort()
			for (let item of languages) {
				if (this.template[item] === undefined) {
					let langConfig = this.allLanguage.languages.find((lang) => {
						return lang.name === item
					})
					if (this.problem.template[item] === undefined) {
						data[item] = {
							checked: false,
							code: langConfig.config.template,
							mode: langConfig.content_type,
						}
					} else {
						data[item] = {
							checked: true,
							code: this.problem.template[item],
							mode: langConfig.content_type,
						}
					}
				} else {
					data[item] = this.template[item]
				}
			}
			this.template = data
		},
		'problem.spj_language'(newVal) {
			this.spjMode = this.allLanguage.spj_languages.find((item) => {
				return item.name === this.problem.spj_language
			}).content_type
		},
	},
	methods: {
        change(val){
			this.content=val;
			console.log(val)  //以html格式获取simditor的正文内容
		},
		switchSpj() {
			if (this.testCaseUploaded) {
				this.$confirm(
					'If you change problem judge method, you need to re-upload test cases',
					'Warning',
					{
						confirmButtonText: 'Yes',
						cancelButtonText: 'Cancel',
						type: 'warning',
					}
				)
					.then(() => {
						this.problem.spj = !this.problem.spj
						this.resetTestCase()
					})
					.catch(() => {})
			} else {
				this.problem.spj = !this.problem.spj
			}
		},
		querySearch(queryString, cb) {
			api.getProblemTagList({ keyword: queryString })
				.then((res) => {
					let tagList = []
					for (let tag of res.data.data) {
						tagList.push({ value: tag.name })
					}
					cb(tagList)
				})
				.catch(() => {})
		},
		resetTestCase() {
			this.testCaseUploaded = false
			this.problem.test_case_score = []
			this.problem.test_case_id = ''
		},
		addTag() {
			let inputValue = this.tagInput
			if (inputValue) {
				this.problem.tags.push(inputValue)
			}
			this.inputVisible = false
			this.tagInput = ''
		},
		closeTag(tag) {
			this.problem.tags.splice(this.problem.tags.indexOf(tag), 1)
		},
		addSample() {
			this.programmingQuestions.input.push('')
			this.programmingQuestions.output.push('')
		},
		deleteSample(index) {
			this.programmingQuestions.input.splice(index, 1)
			this.programmingQuestions.output.splice(index, 1)
		},
		uploadSucceeded(response) {
			if (response.error) {
				this.$error(response.data)
				return
			}
			let fileList = response.data.info
			for (let file of fileList) {
				file.score = (100 / fileList.length).toFixed(0)
				if (!file.output_name && this.problem.spj) {
					file.output_name = '-'
				}
			}
			this.problem.test_case_score = fileList
			this.testCaseUploaded = true
			this.problem.test_case_id = response.data.id
		},
		uploadFailed() {
			this.$error('Upload failed')
		},
		compileSPJ() {
			let data = {
				id: this.problem.id,
				spj_code: this.problem.spj_code,
				spj_language: this.problem.spj_language,
			}
			this.loadingCompile = true
			api.compileSPJ(data).then(
				(res) => {
					this.loadingCompile = false
					this.problem.spj_compile_ok = true
					this.error.spj = ''
				},
				(err) => {
					this.loadingCompile = false
					this.problem.spj_compile_ok = false
					const h = this.$createElement
					this.$msgbox({
						title: 'Compile Error',
						type: 'error',
						message: h('pre', err.data.data),
						showCancelButton: false,
						closeOnClickModal: false,
						customClass: 'dialog-compile-error',
					})
				}
			)
		},
		open3(title) {
			this.$message({
				message: title,
				type: 'warning',
			})
		},
        ifNumber(){
    if(!(/^\d+$/.test(this.programmingQuestions.score))){
 this.open3('分数不能输入非数字')
  this.programmingQuestions.score=""
    }
   

  
        },
		submit() {
            this.programmingQuestions.code_snippets=[]
			for (let i in this.languages1) {
				if (this.languages1[i].open) {
					let template = {
						lang: this.languages1[i].name,
						code: this.languages1[i].value,
					}
					this.programmingQuestions.code_snippets.push(template)
				}
			}
			if (this.programmingQuestions.title == '') {
				this.open3('请填写题目 ')
				return
			}
			if (this.programmingQuestions.description == '') {
				this.open3('请填写描述')
				return
			}
			if (this.programmingQuestions.languages.length == 0) {
				this.open3('请选择可选编程语言')
				return
			}
			if (this.programmingQuestions.code_snippets.length == 0) {
				this.open3('请编写代码片段')
				return
			}
			if (this.programmingQuestions.type == '') {
				this.open3('请编写类型')
				return
			}
			if (this.programmingQuestions.score == '') {
				this.open3('请编写分数')
				return
			}
		   
            if(this.programmingQuestions.type=="coding"){
                delete this.programmingQuestions.spj_code;   
            }else{
                delete this.programmingQuestions.input;
                delete this.programmingQuestions.output;
            }
            //下面将对象转换为json
			this.jsonData = JSON.stringify(this.programmingQuestions)
		},
		//复制的方法
		copy(text) {
			const clipboard = new Clipboard('.copy', {
				text: () => {
					return text
				},
			})
			clipboard.on('success', () => {
				this.$message.success('复制成功')
				clipboard.destroy()
			})
			clipboard.on('error', () => {
				this.$message.error('复制失败')
				clipboard.destroy()
			})
		},
	},
}
</script>

<style lang="less" scoped>
.problem {
	.difficulty-select {
		width: 120px;
	}
	.spj-radio {
		margin-left: 10px;
		&:last-child {
			margin-right: 20px;
		}
	}
	.input-new-tag {
		width: 78px;
	}
	.button-new-tag {
		height: 24px;
		line-height: 22px;
		padding-top: 0;
		padding-bottom: 0;
	}
	.tags {
		.el-tag {
			margin-right: 10px;
		}
	}
	.accordion {
		margin-bottom: 10px;
	}
	.add-samples {
		width: 100%;
		background-color: #fff;
		border: 1px dashed #aaa;
		outline: none;
		cursor: pointer;
		color: #666;
		height: 35px;
		font-size: 14px;
		&:hover {
			background-color: #f9fafc;
		}
		i {
			margin-right: 10px;
		}
	}
	.add-sample-btn {
		margin-bottom: 10px;
	}
}
.json {
	margin: 20px;
	padding: 5px 10px;
}
</style>

<style>
.problem-tag-poper {
	width: 200px !important;
}
.ivu-card {
	padding: 10px;
}
.dialog-compile-error {
	width: auto;
	max-width: 80%;
	overflow-x: scroll;
}
</style>

