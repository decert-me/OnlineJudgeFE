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
						<!-- <el-form-item prop="description" :label="$t('m.Description')" required> -->
						<el-form-item prop="description" label="描述">
							<Simditor v-model="programmingQuestions.description"></Simditor>
						</el-form-item>
					</el-col>
				</el-row>

				<!-- <el-row :gutter="20">
					<el-col :span="8">
						<el-form-item :label="$t('m.Time_Limit') + ' (ms)' " required>
							<el-input type="Number" :placeholder="$t('m.Time_Limit')" v-model="problem.time_limit"></el-input>
						</el-form-item>
					</el-col>
					<el-col :span="8">
						<el-form-item :label="$t('m.Memory_limit') + ' (MB)' " required>
							<el-input type="Number" :placeholder="$t('m.Memory_limit')" v-model="problem.memory_limit"></el-input>
						</el-form-item>
					</el-col>
					<el-col :span="8">
						<el-form-item :label="$t('m.Difficulty')">
							<el-select class="difficulty-select" size="small" :placeholder="$t('m.Difficulty')" v-model="problem.difficulty">
								<el-option :label="$t('m.Low')" value="Low"></el-option>
								<el-option :label="$t('m.Mid')" value="Mid"></el-option>
								<el-option :label="$t('m.High')" value="High"></el-option>
							</el-select>
						</el-form-item>
					</el-col>
				</el-row> -->
				<el-row :gutter="20">
					<!-- <el-col :span="4">
						<el-form-item :label="$t('m.Visible')">
							<el-switch v-model="problem.visible" active-text="" inactive-text="">
							</el-switch>
						</el-form-item>
					</el-col>
					<el-col :span="4">
						<el-form-item :label="$t('m.ShareSubmission')">
							<el-switch v-model="problem.share_submission" active-text="" inactive-text="">
							</el-switch>
						</el-form-item>
					</el-col>
					<el-col :span="8">
						<el-form-item :label="$t('m.Tag')" :error="error.tags" required>
							<span class="tags">
								<el-tag v-for="tag in problem.tags" :closable="true" :close-transition="false" :key="tag" type="success" @close="closeTag(tag)">{{tag}}</el-tag>
							</span>
							<el-autocomplete v-if="inputVisible" size="mini" class="input-new-tag" popper-class="problem-tag-poper" v-model="tagInput" :trigger-on-focus="false" @keyup.enter.native="addTag" @select="addTag" :fetch-suggestions="querySearch">
							</el-autocomplete>
							<el-button class="button-new-tag" v-else size="small" @click="inputVisible = true">+ {{$t('m.New_Tag')}}</el-button>
						</el-form-item>
					</el-col> -->
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
				<!-- <el-form-item style="margin-top: 20px" :label="$t('m.Hint')"> -->
				<!-- <el-form-item style="margin-top: 20px" label="code_snippets">
					<Simditor v-model="programmingQuestions.code_snippets[0].code" placeholder=""></Simditor>
				</el-form-item> -->
				<!-- <el-form-item :label="$t('m.Code_Template')"> -->
				<!-- <el-form-item label="code_snippets">
					<el-row>
					<el-col :span="24" v-for="(v, k) in languages" :key="'template'+k">
							<el-form-item>
								<el-checkbox v-model="v.name">{{ v.name }}</el-checkbox>
								<div v-if="v.checked">
									<code-mirror v-model="v.name" :mode="v.name"></code-mirror>
								</div>
							</el-form-item>
						</el-col>

						<el-checkbox-group>
							<div v-for="lang in languages" :key="'spj'+lang">
							
								<el-radio :label="lang.name" v-model="programmingQuestions.code_snippets[0].lang">{{ lang.name }} </el-radio>
							</div>
						</el-checkbox-group>

					</el-row>
				</el-form-item> -->

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

				<!-- <el-form-item :label="$t('m.Special_Judge')" :error="error.spj"> -->
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
								<!-- <el-tooltip effect="dark" placement="top-start">
								
								</el-tooltip> -->
							</el-radio-group>
							<el-button type="primary" size="small" icon="el-icon-fa-random" @click="compileSPJ" :loading="loadingCompile">
								{{$t('m.Compile')}}
							</el-button>
						</template>
						<code-mirror v-model="programmingQuestions.spj_code" :mode="spjMode"></code-mirror>
					</Accordion>
				</el-form-item>
				<!-- <el-row :gutter="20">
					<el-col :span="6">
						<el-form-item :label="$t('m.TestCase')" :error="error.testcase">
							<el-upload action="/api/admin/test_case" name="file" :data="{spj: problem.spj}" :show-file-list="true" :on-success="uploadSucceeded" :on-error="uploadFailed">
								<el-button size="small" type="primary" icon="el-icon-fa-upload">Choose File</el-button>
							</el-upload>
						</el-form-item>
					</el-col>
                    
					<el-col :span="6">
						<el-form-item :label="$t('m.IOMode')">
							<el-radio-group v-model="problem.io_mode.io_mode">
								<el-radio label="Standard IO">Standard IO</el-radio>
								<el-radio label="File IO">File IO</el-radio>
							</el-radio-group>
						</el-form-item>
					</el-col>

					<el-col :span="4" v-if="problem.io_mode.io_mode == 'File IO'">
						<el-form-item :label="$t('m.InputFileName')" required>
							<el-input type="text" v-model="problem.io_mode.input"></el-input>
						</el-form-item>
					</el-col>
					<el-col :span="4" v-if="problem.io_mode.io_mode == 'File IO'">
						<el-form-item :label="$t('m.OutputFileName')" required>
							<el-input type="text" v-model="problem.io_mode.output"></el-input>
						</el-form-item>
					</el-col>

					<el-col :span="24">
						<el-table :data="problem.test_case_score" style="width: 100%">
							<el-table-column prop="input_name" :label="$t('m.Input')">
							</el-table-column>
							<el-table-column prop="output_name" :label="$t('m.Output')">
							</el-table-column>
							<el-table-column prop="score" :label="$t('m.Score')">
								<template slot-scope="scope">
									<el-input size="small" :placeholder="$t('m.Score')" v-model="scope.row.score" :disabled="problem.rule_type !== 'OI'">
									</el-input>
								</template>
							</el-table-column>
						</el-table>
					</el-col>
				</el-row> -->

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
import Simditor from '../../components/Simditor'
import Accordion from '../../components/Accordion'
import CodeMirror from '../../components/CodeMirror'
import api from '../../api'
import Clipboard from 'clipboard'

export default {
	name: 'Problem',
	components: {
		Simditor,
		Accordion,
		CodeMirror,
	},
	data() {
		return {
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
			asdasdasdas: 'asdasd',
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
				this.open3('请填写题目')
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
		    this.programmingQuestions.description=this.programmingQuestions.description.substring(3,this.programmingQuestions.description.length-4)
            if(this.programmingQuestions.type=="coding"){
                delete this.programmingQuestions.spj_code;   
            }else{
                delete this.programmingQuestions.input;
                delete this.programmingQuestions.output;
            }
            //下面将对象转换为json
			this.jsonData = JSON.stringify(this.programmingQuestions)
			return
			if (!this.problem.samples.length) {
				this.$error('Sample is required')
				return
			}
			for (let sample of this.problem.samples) {
				if (!sample.input || !sample.output) {
					this.$error('Sample input and output is required')
					return
				}
			}
			if (!this.problem.tags.length) {
				this.error.tags = 'Please add at least one tag'
				this.$error(this.error.tags)
				return
			}
			if (this.problem.spj) {
				if (!this.problem.spj_code) {
					this.error.spj = 'Spj code is required'
					this.$error(this.error.spj)
				} else if (!this.problem.spj_compile_ok) {
					this.error.spj =
						'SPJ code has not been successfully compiled'
				}
				if (this.error.spj) {
					this.$error(this.error.spj)
					return
				}
			}
			if (!this.problem.languages.length) {
				this.error.languages =
					'Please choose at least one language for problem'
				this.$error(this.error.languages)
				return
			}
			if (!this.testCaseUploaded) {
				this.error.testCase = 'Test case is not uploaded yet'
				this.$error(this.error.testCase)
				return
			}
			if (this.problem.rule_type === 'OI') {
				for (let item of this.problem.test_case_score) {
					try {
						if (parseInt(item.score) <= 0) {
							this.$error('Invalid test case score')
							return
						}
					} catch (e) {
						this.$error('Test case score must be an integer')
						return
					}
				}
			}
			this.problem.languages = this.problem.languages.sort()
			this.problem.template = {}
			for (let k in this.template) {
				if (this.template[k].checked) {
					this.problem.template[k] = this.template[k].code
				}
			}
			let funcName = {
				'create-problem': 'createProblem',
				'edit-problem': 'editProblem',
				'create-contest-problem': 'createContestProblem',
				'edit-contest-problem': 'editContestProblem',
			}[this.routeName]
			// edit contest problem 时, contest_id会被后来的请求覆盖掉
			if (funcName === 'editContestProblem') {
				this.problem.contest_id = this.contest.id
			}
			api[funcName](this.problem)
				.then((res) => {
					if (
						this.routeName === 'create-contest-problem' ||
						this.routeName === 'edit-contest-problem'
					) {
						this.$router.push({
							name: 'contest-problem-list',
							params: { contestId: this.$route.params.contestId },
						})
					} else {
						this.$router.push({ name: 'problem-list' })
					}
				})
				.catch(() => {})
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

