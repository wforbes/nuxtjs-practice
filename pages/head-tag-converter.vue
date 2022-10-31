<template>
	<div id="head-tag-converter">
		<h1 class="text-3xl">Head Tag Converter</h1>
		<p>
			Convert the &lt;head&gt; tag contents from a HTML file into
			a JavaScript object that can be used by Nuxt.js with the
			<a
				href="https://v3.nuxtjs.org/getting-started/seo-meta#composable-usehead"
				target="_blank"
			>
				useHead() composable function
			</a>.
		</p>
		<div class="my-8">
			<input type="file" accept=".html" @change="onFileChange" />
		</div>
		<div class="mb-8">
			<!--<button @click="convertMarkup" :disabled="files.length === 0">Convert</button>-->
			<!--<input
				type="button"
				value="Convert"
				@click="convertMarkup" 
				:disabled="files.length === 0" 
				class="inline-block px-5 py-2 mx-auto text-white bg-blue-600 rounded-full hover:bg-blue-700 md:mx-0 disabled:opacity-30"
			/>-->
			<TWButton value="Convert" :disabled="files.length === 0" @click="convertMarkup" />
			<span v-if="files.length === 1">
				Ready to convert: <strong>{{ files[0].name }}</strong>
			</span>
			<span v-else>
				Choose an HTML file to convert
			</span>
		</div>
		<div class="columns-2">
			<div class="text-right">
				<textarea ref="outputArea" cols="50" rows="10" class="border-2">{{ output }}</textarea>
			</div>
			<div class="drop-shadow-md round border-2 w-1/3 min-h-full">
				<h3 class="text-xl">Tag counts: <span v-if="convertInfo.length === 0"></span></h3>
				<div v-html="convertInfo" ></div>
			</div>
		</div>
		<div>
			<!-- <button ref="copyBtn" @click="copyOutput">Copy code</button> -->
			<TWButton value="Copy JS Code" :disabled="output.length === 0" @click="copyOutput" />
			<span v-if="showCopyNotify">Text was copied!</span>
		</div>
		<div style="margin-top: 2em">
			<!-- <button @click="reset" :disabled="output.length === 0">Reset Form</button> -->
			<TWButton value="Reset Form" :disabled="output.length === 0" @click="reset" />
		</div>
	</div>
</template>

<script>
export default {
	name: 'HeadTagConverter',
	data() {
		return {
			output: "",
			files: [],
			markupString: [],
			showCopyNotify: false,
			convertInfo: "",
			tagCount: {
				title: false,
				style: false,
				base: false,
				link: 0,
				meta: 0,
				script: 0,
				noscript: 0
			},
		}
	},
	methods: {
		onFileChange(e) {
			this.files = e.target.files;
			if (!this.files.length) return;
			let vm = this;
			for (let i = 0; i < this.files.length; i++) {
				let reader = new FileReader();
				reader.onload = (e) => {
					vm.markupString = e.target.result;
				};
				reader.readAsText(this.files[i]);
			}
		},
		async convertMarkup() {
			// clear HTML comments
			const commentsReg = /<!--(.*?)-->/gm;
			this.markupString = this.markupString.replace(commentsReg, '');

			const parser = new DOMParser();
			const htmlDoc = parser.parseFromString(this.markupString, 'text/html');
			
			const headTag = htmlDoc.getElementsByTagName('head')[0];
			//console.log(headTag);

			const headTagObj = await this.parseMarkup(headTag);
			//console.log(headTagObj);

			this.addOutput(JSON.stringify(headTagObj));
			
			for (let prop in this.tagCount) {
				this.convertInfo += prop + ": " + this.tagCount[prop] + "<br />"
			}
			
			
		},
		parseMarkup(headTag) {
			return new Promise((resolve) => {
				let headTagObj = {};
				for (let child of headTag.children) {
					
					let tagName = child.tagName.toLowerCase();
					//console.log(tagName);
					this.addTagToCount(tagName);

					if (child.attributes.length == 0) {
						headTagObj[tagName] = child.textContent;
						continue;
					}

					let iAttrObj = {};
					for (let i = 0; i < child.attributes.length; i++) {
						//console.log(child.attributes[i].name, child.attributes[i].value);
						iAttrObj[child.attributes[i].name] = child.attributes[i].value;
					}

					if (!headTagObj.hasOwnProperty(tagName))
						headTagObj[tagName] = [];

					headTagObj[tagName].push(iAttrObj);
				}
				resolve(headTagObj);
			});
		},
		addTagToCount(tagName) {
			if (!Object.keys(this.tagCount).includes(tagName.toLowerCase())) return;
			
			if (typeof this.tagCount[tagName] === "boolean") {
				this.tagCount[tagName] = true;
			} else if (typeof this.tagCount[tagName] === "number") {
				this.tagCount[tagName]++;
			}
		},
		addOutput(s) {
			this.output += s+"\n"
		},
		reset() {
			this.output = "";
			this.files = [];
			this.markupString = [];
			this.showCopyNotify = false;
			this.convertInfo = "";
		},
		copyOutput() {
			const outputEl = this.$refs.outputArea;
			outputEl.select();
			outputEl.setSelectionRange(0, 99999);
			navigator.clipboard.writeText(outputEl.value);
			this.showCopyNotify = true;
			setTimeout(() => {
				this.showCopyNotify = false;
			}, 3000)
			this.$refs.copyBtn.focus();
		}
	}
}
</script>