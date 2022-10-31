<template>
	<div id="head-tag-converter">
		<h1>Head Tag Converter</h1>
		<p>
			Convert the &lt;head&gt; tag contents from a .html file into
			a JavaScript object that can be used by Nuxt.js with the
			<a
				href="https://v3.nuxtjs.org/getting-started/seo-meta#composable-usehead"
				target="_blank"
			>
				useHead() composable function
			</a>.
		</p>
		<div style="margin-bottom:2em;">
			<input type="file" accept=".html" @change="onFileChange" />
		</div>
		<div style="margin-bottom: 1em">
			<button @click="convertMarkup" :disabled="files.length === 0">Convert</button>	
			<span v-if="files.length === 1">
				Ready to convert: <strong>{{ files[0].name }}</strong>
			</span>
		</div>
		<div>
			<textarea ref="outputArea" cols="50" rows="10">{{ output }}</textarea>
		</div>
		<div>
			<button ref="copyBtn" @click="copyOutput">Copy code</button>
			<span v-if="showCopyNotify">Text was copied!</span>
		</div>
		<div style="margin-top: 2em">
			<button @click="reset" :disabled="output.length === 0">Reset Form</button>
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
			showCopyNotify: false
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
		},
		parseMarkup(headTag) {
			return new Promise((resolve) => {
				let headTagObj = {};
				for (let child of headTag.children) {
					
					let tagName = child.tagName.toLowerCase();
					//console.log(tagName);

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
		addOutput(s) {
			this.output += s+"\n"
		},
		reset() {
			this.output = "";
			this.files = [];
			this.markupString = [];
			this.showCopyNotify = false;
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