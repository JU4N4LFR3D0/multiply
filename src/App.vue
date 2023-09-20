<template>
	<v-container fluid>
		<v-dialog v-model:model-value="finish_dialog" width="100%" max-width="600px" height="max-content">
			<v-card :color="score > 0 ? '#9DC058' : 'red-lighten-2'">
				<v-card-text>
					<v-row no-gutters>
						<v-col cols="4">
							<v-img v-if="score > 0" src="src/assets/2.png" height="300px" class="ma-2" />
							<v-img v-else src="src/assets/3.png" height="300px" class="ma-2" />
						</v-col>
						<v-col cols="8" class="d-flex align-center justify-center flex-column">
							<div class="text-h3 text-center">
								Tu puntuación es: <span class="text-h2">
									{{ score }}
								</span>
							</div>
							<div class="text-h6 text-center mt-2">
								Record: <span class="text-h5 font-weight-bold">
									{{ top }}
								</span>
							</div>
						</v-col>
					</v-row>
				</v-card-text>
			</v-card>
		</v-dialog>
		<v-row class="py-4">
			<v-col cols="12" lg="6" class="d-flex align-center justify-center">
				<v-img :src="'src/assets/title.png'" max-height="90px" max-width="70%" />
				<v-btn @click="share()" icon="mdi-share-variant" variant="text" />
			</v-col>
			<v-col cols="12" lg="6" class="d-flex flex-column flex-lg-row justify-center align-center">
				<v-btn-toggle v-model="difficulty" mandatory density="compact" variant="outlined">
					<v-btn v-for="(item, index) in difficulties" :key="index" :value="item.value" :color="item.color">
						<div class="d-flex d-sm-none">
							{{ item.alt_text }}
						</div>
						<div class="d-none d-sm-flex">
							{{ item.text }}
						</div>
					</v-btn>
				</v-btn-toggle>
				<v-chip class="pr-0 py-4 ma-4 text-h6" color="#7E9F37">
					Puntuación
					<v-chip color="#9DC058" class="ml-2 text-h5 font-weight-bold">
						{{ score }}
					</v-chip>
				</v-chip>
			</v-col>
		</v-row>
		<v-tabs v-model="operation" align-tabs="center" bg-color="#9DC058" color="white">
			<v-tab v-for="(item, index) in operations" :key="index" :value="item.value" :color="item.color">
				<div class="d-none d-sm-flex">
					{{ item.text }}
				</div>
				<v-icon class="d-flex d-sm-none" :icon="item.icon" />
			</v-tab>
		</v-tabs>
		<v-row class="mt-4">
			<v-col cols="12" md="9" class="d-flex align-center justify-center">
				<v-btn icon color="#7E9F37" :size="size" class="d-none d-md-flex ma-2 text-h1">
					{{ first }}
				</v-btn>
				<v-btn icon color="#7E9F37" :size="size * rsize" class="d-flex d-md-none ma-2 text-h3">
					{{ first }}
				</v-btn>
				<v-btn icon :color="operations[operation].color" :size="size" class="d-none d-md-flex ma-2">
					<v-icon :size="size * 0.6">
						{{ operations[operation].icon }}
					</v-icon>
				</v-btn>
				<v-btn icon :color="operations[operation].color" :size="size * rsize" class="d-flex d-md-none ma-2">
					<v-icon :size="size * 0.6 * rsize">
						{{ operations[operation].icon }}
					</v-icon>
				</v-btn>
				<v-btn icon color="#7E9F37" :size="size" class="d-none d-md-flex ma-2 text-h1">
					{{ second }}
				</v-btn>
				<v-btn icon color="#7E9F37" :size="size * rsize" class="d-flex d-md-none ma-2 text-h3">
					{{ second }}
				</v-btn>
			</v-col>
			<v-col cols="12" md="3" class="d-flex align-center justify-center">
				<v-img src="src/assets/4.png" class="d-none d-md-flex" max-height="300px" />
				<v-img src="src/assets/4.png" class="d-flex d-md-none" max-height="150px" />
			</v-col>
		</v-row>
		<v-divider class="mt-4 mb-6" />
		<v-row justify="center">
			<v-col cols="6" sm="4" md="3" v-for="(item, index) in options" :key="index"
				class="d-flex align-center justify-center">
				<v-btn @click="answer(item)" icon :color="randColor()" :size="size * rsize * 1.2"
					class="d-none d-md-flex ma-2 text-h2">
					{{ item }}
				</v-btn>
				<v-btn @click="answer(item)" icon :color="randColor()" :size="size * rsize * 0.9"
					class="d-flex d-md-none ma-2 text-h4">
					{{ item }}
				</v-btn>
			</v-col>
		</v-row>
		<v-divider class="mt-12 mb-6" />
		<v-row>
			<v-col class="text-center">
				<div class="d-inline-block mx-1" v-for="(item, index) in links" :key="index">
					<v-tooltip :text="item.text">
						<template v-slot:activator="{ props }">
							<v-btn v-bind="props" :href="item.link" target="_blank" rel="noopener noreferrer" :icon="item.icon"
								:color="item.color" />
						</template>
					</v-tooltip>
				</div>
			</v-col>
		</v-row>
	</v-container>
</template>

<script>

const suma = 0;
const resta = 1;
const multiplicacion = 2;
const division = 3;

const correct_audio = new Audio("src/assets/sound/correct.mp3");
correct_audio.volume = 0.5;

const wrong_audio = new Audio("src/assets/sound/wrong.mp3");

const colors = [
	'red-accent-4',
	'pink-accent-3',
	'purple',
	'deep-purple-accent-4',
	'indigo-darken-4',
	'blue',
	'light-blue-accent-3',
	'cyan',
	'teal',
	'green',
	'green-accent-4',
	'light-green',
	'lime-accent-4',
	'yellow-accent-3',
	'amber-darken-1',
	'orange-accent-4',
	'deep-orange-accent-3',
];

const getNArray = (n) => [...Array(n).keys()];
const getN1Array = (n) => Array.from({ length: n }, (_, i) => i + 1);
const randElement = (arr) => arr[Math.floor(Math.random() * arr.length)];
const shuffle = (arr) => arr.sort(() => (Math.random() > .5) ? 1 : -1);

export default {
	data: function () {
		return {
			//UI
			finish_dialog: false,
			size: 230,
			rsize: 0.5,

			//State
			score: 0,
			first: 0,
			second: 0,
			res: 0,
			top: 0,
			options: [],
			operation: multiplicacion,
			difficulty: 0,

			//Const
			operations: [
				{
					text: "Sumas",
					icon: "mdi-plus",
					color: "success",
					value: suma
				},
				{
					text: "Restas",
					icon: "mdi-minus",
					color: "error",
					value: resta
				},
				{
					text: "Multiplicaciones",
					icon: "mdi-close",
					color: "blue",
					value: multiplicacion
				},
				{
					text: "Divisiones",
					icon: "mdi-division",
					color: "warning",
					value: division
				},
			],
			difficulties: [
				{
					value: 0,
					factor: 10,
					dummy: 2,
					score: 1,
					variation: 5,
					text: "Fácil",
					alt_text: "😊",
					color: "teal",
				},
				{
					value: 1,
					factor: 20,
					dummy: 3,
					score: 2,
					variation: 20,
					text: "Normal",
					alt_text: "😎",
					color: "green",
				},
				{
					value: 2,
					factor: 50,
					dummy: 4,
					score: 3,
					variation: 30,
					text: "Difícil",
					alt_text: "🤓",
					color: "orange",
				},
				{
					value: 3,
					factor: 100,
					dummy: 5,
					score: 5,
					variation: 50,
					text: "Extrema",
					alt_text: "😈",
					color: "red",
				},
			],

			//Redes
			links: [
				{
					link: "https://jfourcee.web.app/#/",
					text: "Mi pagina",
					icon: "mdi-web",
					color: "orange-darken-4",
				},
				{
					link: "https://github.com/JU4N4LFR3D0/multiply",
					text: "Repositorio de este proyecto",
					icon: "mdi-github",
					color: "#171515",
				},
				{
					link: "https://www.youtube.com/channel/UCEdn80bp9gmYhrW2VQ27sYg",
					text: "Mi canal",
					icon: "mdi-youtube",
					color: "#FF0000",
				},
				{
					link: "https://www.facebook.com/ju4n4lfr3d0",
					text: "Mi Facebook",
					icon: "mdi-facebook",
					color: "#0766FF",
				},
				{
					link: "https://www.instagram.com/juanalfredocc22/",
					text: "Mi Instagram",
					icon: "mdi-instagram",
					color: "#E1306C",
				},
			]
		}
	},
	methods: {
		//Set score = 0 y genera nueva pregunta
		reset: function () {
			this.score = 0;
			this.question();
		},
		//Genera una nueva pregunta en base a la dificultad y el tipo de operación;
		question: function () {
			const difficulty = this.difficulties[this.difficulty];
			const operation = this.operation;
			const factor = difficulty.factor;
			const arr = operation == division ? getN1Array(factor) : getNArray(factor); // Es division de cero
			//QUESTION
			if (operation == division) {
				this.first = randElement(arr);
				this.second = randElement(arr);
				this.first = this.first * this.second;
			} else {
				this.first = randElement(arr);
				this.second = randElement(arr);
			}
			//ANSWER
			const options = [];
			let res = 0;
			switch (operation) {
				case suma:
					res = this.first + this.second;
					break;
				case resta:
					res = this.first - this.second;
					break;
				case multiplicacion:
					res = this.first * this.second;
					break;
				case division:
					res = this.first / this.second;
					break;
				default:
					res = this.first + this.second;
					break;
			}
			options.push(res);
			//DUMMY ANSERS
			let dummy_first = 0;
			let dummy_second = 0;
			let overflow = 0;
			while (options.length < difficulty.dummy + 1) {

				overflow++;

				if (operation == division) {
					dummy_first = randElement(arr);
					dummy_second = randElement(arr);
					dummy_first = dummy_first * dummy_second;
				} else {
					dummy_first = randElement(arr);
					dummy_second = randElement(arr);
				}

				let dummy_res = 0;
				switch (operation) {
					case suma:
						dummy_res = dummy_first + dummy_second;
						break;
					case resta:
						dummy_res = dummy_first - dummy_second;
						break;
					case multiplicacion:
						dummy_res = dummy_first * dummy_second;
						break;
					case division:
						dummy_res = dummy_first / dummy_second;
						break;
					default:
						dummy_res = dummy_first + dummy_second;
						break;
				}

				if ((Math.abs(res - dummy_res) <= difficulty.variation) && (!options.some((el) => el == dummy_res))) {
					options.push(dummy_res);
				} else if (overflow > 100) {
					options.push(dummy_res);
				}

			}
			//FINISH
			this.options = shuffle(options);
			this.res = res;
		},
		//Esta funcion evalua la respuesta del usuario
		answer: function (val) {
			if (val == this.res) {
				this.score++;
				this.question();
				correct_audio.play();
			} else {
				if(localStorage.getItem('top_score')){
					let top = +localStorage.getItem('top_score');
					if(top <= this.score){
						localStorage.setItem('top_score', this.score);
						top = this.score;
					}
					this.top = top;
				} else {
					localStorage.setItem('top_score', this.score);
					this.top = this.score;
				}
				this.finish_dialog = true;
				wrong_audio.play();
			}
		},
		//Get Random Color
		randColor: function () {
			return randElement(colors);
		},
		//Share page link
		share: function () {
			if (!('share' in navigator)) {
				return;
			}
			const shareData = {
				text: 'Practica las operaciones basicas con Multiply! Gratis y sin anuncios! ' + window.location.href,
			}
			if (navigator.canShare(shareData)) {
				try {
					navigator.share(shareData)
				} catch (err) {
					if (err.name !== 'AbortError') {
						console.error(err.name, err.message)
					}
				}
			} else {
				console.warn('Sharing not supported', shareData)
			}
		}
	},
	watch: {
		//Cambiar la dificultad reinicia el marcador
		difficulty: function () {
			this.reset();
		},
		//Cambiar el tipo de operacion reinicia el marcador
		operation: function () {
			this.reset();
		},
		//Despues de mostrarse el dialogo se reinicia el marcador
		finish_dialog: function () {
			if (this.finish_dialog == false) {
				this.reset();
			}
		}
	},
	// Al cargar la pagina se inicia la primera pregunta
	mounted: function () {
		this.reset();
	}
}
</script>