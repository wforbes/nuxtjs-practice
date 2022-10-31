<template>
<h1>{{ iKnowWhatImDoing }}</h1>
</template>

<script setup>
// playing with useHead to define page 
useHead({
	"meta":[
		{"charset":"utf-8"},
		{"content":"width=device-width, initial-scale=1.0", "name":"viewport"},
		{"content":"", "name":"keywords"},
		{"content":"", "name":"description"}
	],
	"title":"CryptoCoin - Free Cryptocurrency Website Template",
	"link":[
		//{"href":"img/favicon.ico","rel":"icon"},
		{"rel":"preconnect","href":"https://fonts.googleapis.com"},
		{"rel":"preconnect","href":"https://fonts.gstatic.com","crossorigin":""},
		{"href":"https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;500&family=Roboto:wght@500;700&display=swap","rel":"stylesheet"},
		{"href":"https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.10.0/css/all.min.css","rel":"stylesheet"},
		{"href":"https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.1/font/bootstrap-icons.css","rel":"stylesheet"},
		//{"href":"lib/animate/animate.min.css","rel":"stylesheet"},
		//{"href":"lib/owlcarousel/assets/owl.carousel.min.css","rel":"stylesheet"},
		//{"href":"css/bootstrap.min.css","rel":"stylesheet"},
		//{"href":"css/style.css","rel":"stylesheet"}
	]
});
const iKnowWhatImDoing = 'hey it works!'

</script>