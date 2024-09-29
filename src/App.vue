<script setup lang="ts">
import { computed, reactive, ref } from 'vue'

const name = ref('')
const number = ref<number[]>(Array(16))
const cvc = ref('')
const date = reactive({ month: '', year: '' })
const errors = reactive({
	name: '',
	number: '',
	cvc: '',
	date: '',
})

const numberInput = computed({
	get() {
		const numberToDisplay: string[] = []
		number.value.forEach((n, i) => {
			if (i === 4 || i === 8 || i === 12) numberToDisplay.push(' ')

			numberToDisplay.push(n.toString())
		})
		return numberToDisplay.join('')
	},
	set(newValue: string) {
		const newNumber = newValue
			.replaceAll(' ', '')
			.replace(/\D/g, '')
			.slice(0, 16)
			.split('')
			.map(n => parseInt(n))
		newNumber.length = 16
		number.value = newNumber
	},
})

const numberDisplay = computed(() => {
	let numberClone: number[] = [...number.value]
	const numberDisplay: string[] = []

	numberClone = numberClone.map(n => n || 0)

	numberClone.forEach((n, i) => {
		if (i === 4 || i === 8 || i === 12) numberDisplay.push(' ')

		numberDisplay.push(n.toString())
	})

	return numberDisplay.join('')
})

function validateValue(input: 'name' | 'number' | 'date' | 'cvc') {
	if (input == 'name') {
		if (name.value.length == 0) errors.name = 'Nome não pode ser vazio'
		else if (name.value.length < 5) errors.name = 'Nome deve ter pelo menos 5 caracteres'
		else errors.name = ''
	} else if (input == 'number') {
		if (number.value.some(n => n == null || n == undefined)) errors.number = 'Número não pode ser vazio'
		else if (number.value.length != 16) errors.number = 'Número deve ter 16 dígitos'
		else errors.number = ''
		console.log(number.value)
	} else if (input == 'date') {
		// if (date.value.length != 4) errors.date = 'Data deve ter 2 dígitos'
		// else if (date.value.some(n => n == null || n == undefined)) errors.date = 'Data não pode ser vazio'
		// else errors.date = ''
	} else if (input == 'cvc') {
		if (cvc.value.length == 0) errors.cvc = 'CVC não pode ser vazio'
		else if (cvc.value.length < 3) errors.cvc = 'CVC deve ter 3 dígitos'
		else errors.cvc = ''
	}
}
</script>
<template>
	<section id="Cards">
		<div id="FrontCard">
			<p id="CardNumber" v-text="numberDisplay"></p>
			<p id="CardName" v-text="name || 'Nome no cartão'"></p>
			<p id="CardDate">{{ date.month }} / {{ date.year }}</p>
			<img src="./assets/images/bg-card-front.png" alt="FrontCard" />
		</div>
		<div id="BackCard">
			<p id="CardCVC" v-text="cvc"></p>
			<img src="./assets/images/bg-card-back.png" alt="BackCard" />
		</div>
	</section>
	<section id="Inputs">
		<div id="Name">
			<label for="Name-Input">Nome no cartão</label>
			<input
				id="Name-Input"
				type="text"
				v-model="name"
				@blur="validateValue('name')"
				placeholder="Ana Maria"
				:class="{ hasError: errors.name.length > 0 }"
			/>
			<p v-text="errors.name"></p>
		</div>
		<div id="Number">
			<label for="Number-Input">Número no cartão</label>
			<input
				id="Number-Input"
				type="text"
				v-model="numberInput"
				@blur="validateValue('number')"
				placeholder="1234 5678 9123 0000"
				:class="{ hasError: errors.number.length > 0 }"
			/>
			<p v-text="errors.number"></p>
		</div>
		<div>
			<div id="Date">
				<label>Data de expiração (MM/AA)</label>
				<div>
					<input
						id="Month-Input"
						type="number"
						v-model="date.month"
						@blur="validateValue('date')"
						placeholder="MM"
						min="1"
						max="12"
						maxlength="2"
						:class="{ hasError: errors.date.length > 0 }"
					/>
					<input
						id="Year-Input"
						type="number"
						v-model="date.year"
						@blur="validateValue('date')"
						placeholder="AA"
						maxlength="2"
						:class="{ hasError: errors.date.length > 0 }"
					/>
				</div>
				<p v-text="errors.date"></p>
			</div>
			<div id="CVC">
				<label for="CVC-Input">CVC</label>
				<input
					id="CVC-Input"
					type="number"
					v-model="cvc"
					@blur="validateValue('cvc')"
					placeholder="123"
					maxlength="3"
					:class="{ hasError: errors.cvc.length > 0 }"
				/>
				<p v-text="errors.cvc"></p>
			</div>
		</div>
	</section>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&display=swap');

* {
	box-sizing: border-box;
	padding: 0;
	margin: 0;
	font-family: 'Space Grotesk', sans-serif;
	font-weight: 500;
}

#app {
	display: flex;
	align-items: center;
	justify-content: center;
	height: 100vh;
	width: 100vw;
}

#Cards {
	width: 320px;
	height: 100%;
	background-image: url(./assets/images/bg-main-desktop.png);
	background-repeat: no-repeat;
	background-size: cover;
	display: flex;
	flex-direction: column;
	justify-content: center;
	gap: 32px;
	color: hsl(0, 0%, 100%);

	#CardNumber {
		width: fit-content;
		font-size: clamp(1.5rem, 1.5vw, 2.5rem);
		position: absolute;
		bottom: 28%;
		font-weight: 400;
		left: 10%;
	}

	#FrontCard {
		left: 5vw;

		#CardName {
			font-size: clamp(0.8rem, 0.8vw, 2rem);
			position: absolute;
			font-weight: 400;
			text-transform: uppercase;
			bottom: 10%;
			left: 10%;
		}

		#CardDate {
			font-size: clamp(0.8rem, 0.8vw, 2rem);
			position: absolute;
			font-weight: 400;
			text-transform: uppercase;
			bottom: 10%;
			right: 10%;
		}
	}

	#BackCard {
		left: 10vw;

		#CardCVC {
			font-size: clamp(0.8rem, 0.8vw, 2rem);
			position: absolute;
			font-weight: 400;
			text-transform: uppercase;
			left: unset;
			right: 40px;
			top: 45%;
		}
	}

	> div {
		width: 22vw;
		position: relative;
		min-width: 320px;
	}

	> div > img {
		width: 100%;
		height: 100%;
	}
}

#Inputs {
	width: 100%;
	height: 100%;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	padding-left: 80px;

	p {
		font-size: 13px;
		font-weight: 500;
		color: hsl(0, 100%, 66%);
		margin-top: 8px;
	}

	> div {
		min-width: 240px;
		max-width: 320px;
		width: calc(100% - 96px);
		margin-bottom: 16px;
	}

	label {
		display: block;
		text-transform: uppercase;
		font-size: 12px;
		margin-bottom: 4px;
		color: hsl(278, 68%, 11%);
	}

	input {
		width: 100%;
		height: 48px;
		border: 2px solid hsl(270, 3%, 87%);
		border-radius: 8px;
		padding: 0 16px;
		font-size: 16px;
		color: hsl(278, 68%, 11%);
		background-color: #fff;
		box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
		transition: all 0.2s ease-in-out;
		outline: none;

		&#Month-Input,
		&#Year-Input {
			width: 50%;
		}

		&::placeholder {
			color: hsl(270, 3%, 87%);
		}

		&:focus {
			border: 2px solid hsl(279, 6%, 55%);
		}

		&.hasError {
			border: 2px solid hsl(0, 100%, 66%);
		}
	}

	#Name,
	#Number {
		width: 100%;
	}

	#CVC {
		width: 50%;
	}
}

#Date {
	margin-bottom: 16px;
	div {
		display: flex;
		gap: 16px;
	}
}

@media (max-width: 640px) {
	#app {
		flex-direction: column;
	}

	#Cards {
		background-image: url(./assets/images/bg-main-mobile.png);
		width: 100%;
		height: 32%;
		flex-direction: column-reverse;
		align-items: center;
		gap: unset;
		padding-top: 180px;

		h3 {
			font-size: clamp(1.25rem, 4vw, 2rem);
		}

		#FrontCard {
			left: -48px;
			top: -92px;
			z-index: 2;
		}

		#BackCard {
			left: 48px;
			z-index: 1;
		}

		> div {
			width: 60vw;
		}
	}

	#Inputs {
		padding-top: 64px;
		padding-left: unset;
	}
}
</style>
