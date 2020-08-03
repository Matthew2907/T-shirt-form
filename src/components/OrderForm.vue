<template>
	<div id="signup">
		<div class="signup-form">
			<header style="font-size: 24px;" key="headerPart">{{
				stepPage === 8 ? 'Dziękujęmy za zakupy w naszym sklepie' : 'Formularz zamówienia'
			}}</header>
			<div key="progressPart" class="progress-bar" style="background-color: transparent;">
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 2}">
						<span>1</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 3}">
						<span>2</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 4}">
						<span>3</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 5}">
						<span>4</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 6}">
						<span>5</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 7}">
						<span>6</span>
					</div>
				</div>
				<div class="step">
					<div class="bullet" :class="{'step-done': stepPage >= 8}">
						<span>7</span>
					</div>
				</div>
			</div>
			<form key="formPart" @submit.prevent="onSubmit">
				<div class="step-container" v-show="stepPage === 1">
					<div class="input">
						<label style="font-weight: bold" for="imagePosition"
							>Wybór miejsca nadruku</label
						>
						<select class="form-control" id="imagePosition" v-model="imagePosition">
							<option value="przód">przód</option>
							<option value="tył">tył</option>
							<option value="przód i tył">przód i tył</option>
						</select>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 2">
					<p style="font-weight: bold">Wybór grafiki</p>
					<div class="draw-image-container">
						<img :src="imgUrl" alt="t-shirt logo" />
					</div>
					<div class="buttons-container btn-group" role="group">
						<button
							class="btn btn-primary"
							:disabled="
								currentImgId === idsArr[0] || typeof idsArr[0] === 'undefined'
							"
							type="button"
							@click="handlePrevImgButton"
							>Poprzednia</button
						>
						<button
							class="btn btn-primary"
							:disabled="currentImgId === idsArr[1]"
							type="button"
							@click="handleNextImgButton"
							>Następna</button
						>
						<button class="btn btn-primary" type="button" @click="handleDrawButton"
							>Losuj</button
						>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 3">
					<div class="input">
						<label style="font-weight: bold;" for="imageStyle"
							>Wybór stylu grafiki</label
						>
						<select class="form-control" id="imageStyle" v-model="imageStyle">
							<option value="normalny">normalny</option>
							<option value="skala szarości">skala szarości</option>
							<option value="rozmycie">rozmycie</option>
						</select>
						<div class="blur-scale" v-if="imageStyle === 'rozmycie'">
							<input
								style="margin-top: 15px"
								class="custom-range"
								type="range"
								id="volume"
								name="volume"
								min="1"
								max="10"
								step="1"
								list="tickmarks"
								v-model="blurValue"
							/>
							<label style="font-weight: bold;" for="volume">Poziom rozmycia</label>
						</div>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 4">
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepOne}"
					>
						<div class="step-information-content">
							<p
								class="step-information-text"
								style="font-weight: bold; margin-top: 15px;"
								>Wybór miejsca nadruku: {{ imagePosition }}</p
							>
						</div>
						<div class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepOne')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(1)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepTwo}"
					>
						<div class="step-information-content">
							<p class="step-information-text" style="font-weight: bold"
								>Wybór grafiki:
							</p>
							<div class="draw-image-container">
								<img :src="imgUrl" alt="t-shirt logo" />
							</div>
						</div>
						<div style="padding-top: 50px" class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepTwo')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(2)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepThree}"
					>
						<div class="step-information-content">
							<p
								class="step-information-text"
								style="font-weight: bold; margin-top: 30px;"
								>Wybór stylu grafiki: {{ imageStyle }}
								{{ imageStyle === 'rozmycie' ? blurValue : '' }}</p
							>
						</div>
						<div class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepThree')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(3)"
								>Edytuj</button
							>
						</div>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 5">
					<div class="input" :class="{invalid: $v.firstName.$error}">
						<label for="firstName">Imię*</label>
						<input
							type="text"
							id="firstName"
							v-model="firstName"
							@blur="$v.firstName.$touch()"
						/>
						<p v-if="$v.firstName.$error">To pole jest wymagane.</p>
					</div>
					<div class="input" :class="{invalid: $v.lastName.$error}">
						<label for="lastName">Nazwisko*</label>
						<input
							type="text"
							id="lastName"
							v-model="lastName"
							@blur="$v.lastName.$touch()"
						/>
						<p v-if="$v.lastName.$error">To pole jest wymagane.</p>
					</div>
					<div class="input" :class="{invalid: $v.street.$error}">
						<label for="street">Ulica*</label>
						<input
							type="text"
							id="street"
							v-model="street"
							@blur="$v.street.$touch()"
						/>
						<p v-if="$v.street.$error">To pole jest wymagane.</p>
					</div>
					<div class="input" :class="{invalid: $v.buildingNumber.$error}">
						<label for="buildingNumber">Numer budynku*</label>
						<input
							type="number"
							id="buildingNumber"
							v-model.number="buildingNumber"
							@blur="$v.buildingNumber.$touch()"
						/>
						<p v-if="$v.buildingNumber.$error">To pole jest wymagane.</p>
					</div>
					<div class="input">
						<label for="apartmentNumber">Numer mieszkania</label>
						<input
							type="number"
							id="apartmentNumber"
							v-model.number="apartmentNumber"
						/>
					</div>
					<div class="input" :class="{invalid: $v.zipCode.$error}">
						<label for="zipCode">Kod pocztowy*</label>
						<input
							type="number"
							id="zipCode"
							v-model.number="zipCode"
							@blur="$v.zipCode.$touch()"
						/>
						<p v-if="$v.zipCode.$error">To pole jest wymagane.</p>
					</div>

					<div class="input" :class="{invalid: $v.city.$error}">
						<label for="city">Miasto*</label>
						<input type="text" id="city" v-model="city" @blur="$v.city.$touch()" />
						<p v-if="$v.city.$error">To pole jest wymagane.</p>
					</div>
					<div class="input">
						<label for="telephonNumber">Numer telefonu</label>
						<input type="number" id="telephonNumber" v-model.number="telephonNumber" />
					</div>
					<div class="input" :class="{invalid: $v.email.$error}">
						<label for="email">Email*</label>
						<input type="email" id="email" v-model="email" @blur="$v.email.$touch()" />
						<p v-if="!$v.email.email">Wprowadź prawidłowy email.</p>
						<p v-else-if="$v.email.$error">To pole jest wymagane.</p>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 6">
					<div class="radios-shipping-container">
						<label for="personalPickup">
							<input
								type="radio"
								id="personalPickup"
								value="odbiór osobisty"
								v-model="shippingMethod"
								@change="$v.shippingMethod.$touch()"
							/>
							Odbiór osobisty
						</label>
						<label for="shipping">
							<input
								type="radio"
								id="shipping"
								value="wysyłka"
								v-model="shippingMethod"
								@change="$v.shippingMethod.$touch()"
							/>
							Wysyłka
						</label>
					</div>
					<div class="shipping-address-container" v-if="shippingMethod === 'wysyłka'">
						<button class="btn btn-primary" type="button" @click="handleShippingInputs"
							>Adres taki sam jak adres rachunku</button
						>
						<div class="input" :class="{invalid: $v.shippingFirstName.$error}">
							<label for="shippingFirstName">Imię*</label>
							<input
								type="text"
								id="shippingFirstName"
								v-model="shippingFirstName"
								@blur="$v.shippingFirstName.$touch()"
							/>
							<p v-if="$v.shippingFirstName.$error">To pole jest wymagane.</p>
						</div>
						<div class="input" :class="{invalid: $v.shippingLastName.$error}">
							<label for="shippingLastName">Nazwisko*</label>
							<input
								type="text"
								id="shippingLastName"
								v-model="shippingLastName"
								@blur="$v.shippingLastName.$touch()"
							/>
							<p v-if="$v.shippingLastName.$error">To pole jest wymagane.</p>
						</div>
						<div class="input" :class="{invalid: $v.shippingStreet.$error}">
							<label for="shippingStreet">Ulica*</label>
							<input
								type="text"
								id="shippingStreet"
								v-model="shippingStreet"
								@blur="$v.shippingStreet.$touch()"
							/>
							<p v-if="$v.shippingStreet.$error">To pole jest wymagane.</p>
						</div>
						<div class="input" :class="{invalid: $v.shippingBuildingNumber.$error}">
							<label for="shippingBuildingNumber">Numer budynku*</label>
							<input
								type="number"
								id="shippingBuildingNumber"
								v-model.number="shippingBuildingNumber"
								@blur="$v.shippingBuildingNumber.$touch()"
							/>
							<p v-if="$v.shippingBuildingNumber.$error">To pole jest wymagane.</p>
						</div>
						<div class="input">
							<label for="shippingApartmentNumber">Numer mieszkania</label>
							<input
								type="number"
								id="shippingApartmentNumber"
								v-model.number="shippingApartmentNumber"
							/>
						</div>
						<div class="input" :class="{invalid: $v.shippingZipCode.$error}">
							<label for="shippingZipCode">Kod pocztowy*</label>
							<input
								type="number"
								id="shippingZipCode"
								v-model.number="shippingZipCode"
								@blur="$v.shippingZipCode.$touch()"
							/>
							<p v-if="$v.shippingZipCode.$error">To pole jest wymagane.</p>
						</div>
						<div class="input" :class="{invalid: $v.shippingCity.$error}">
							<label for="shippingCity">Miasto*</label>
							<input
								type="text"
								id="shippingCity"
								v-model="shippingCity"
								@blur="$v.shippingCity.$touch()"
							/>
							<p v-if="$v.shippingCity.$error">To pole jest wymagane.</p>
						</div>
						<div class="input">
							<label for="shippingTelephonNumber">Numer telefonu</label>
							<input
								type="number"
								id="shippingTelephonNumber"
								v-model.number="shippingTelephonNumber"
							/>
						</div>
						<div class="input" :class="{invalid: $v.shippingEmail.$error}">
							<label for="shippingEmail">Email</label>
							<input
								type="email"
								id="shippingEmail"
								v-model="shippingEmail"
								@blur="$v.shippingEmail.$touch()"
							/>
							<p v-if="!$v.shippingEmail.email">Wprowadź prawidłowy email.</p>
							<p v-else-if="$v.shippingEmail.$error">To pole jest wymagane.</p>
						</div>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 7">
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepOne}"
					>
						<div class="step-information-content">
							<p
								style="font-weight: bold; margin-top: 30px;"
								class="step-information-text"
								>Wybór miejsca nadruku: {{ imagePosition }}</p
							>
						</div>
						<div class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepOne')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(1)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepTwo}"
					>
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Wybór grafiki:
							</p>
							<div class="draw-image-container">
								<img :src="imgUrl" alt="t-shirt logo" />
							</div>
						</div>
						<div class="buttons-container" style="padding-top: 50px">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepTwo')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(2)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepThree}"
					>
						<div class="step-information-content">
							<p
								class="step-information-text"
								style="font-weight: bold; margin-top: 30px;"
								>Wybór stylu grafiki: {{ imageStyle }}
								{{ imageStyle === 'rozmycie' ? blurValue : '' }}</p
							>
						</div>
						<div class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepThree')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(3)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepFive}"
					>
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Dane klienta:
							</p>
							<p>Imię: {{ firstName }}</p>
							<p>Nazwisko: {{ lastName }}</p>
							<p>Ulica: {{ street }}</p>
							<p>Numer budynku: {{ buildingNumber }}</p>
							<p>Numer mieszkania: {{ apartmentNumber }}</p>
							<p>Kod pocztowy: {{ zipCode }}</p>
							<p>Miasto: {{ city }}</p>
							<p>Numer telefonu: {{ telephonNumber }}</p>
							<p>Email: {{ email }}</p>
						</div>
						<div style="padding-top: 150px;" class="buttons-container">
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepFive')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(5)"
								>Edytuj</button
							>
						</div>
					</div>
					<div
						class="step-control-panel"
						:class="{'confirmed-field': confirmInfoParts.stepSix}"
					>
						<div class="step-information-content">
							<p
								:class="{
									addMoreSpaceToShippingSummary: shippingMethod !== 'wysyłka',
								}"
								style="font-weight: bold;"
								>Sposób odbioru: {{ shippingMethod }}</p
							>
							<div
								class="shipping-information-content"
								v-if="shippingMethod === 'wysyłka'"
							>
								<p style="font-weight: bold;" class="step-information-text"
									>Dane odbiorcy:
								</p>
								<p>Imię: {{ shippingFirstName }}</p>
								<p>Nazwisko: {{ shippingLastName }}</p>
								<p>Ulica:{{ shippingStreet }}</p>
								<p>Numer budynku: {{ shippingBuildingNumber }}</p>
								<p>Numer mieszkania: {{ shippingApartmentNumber }}</p>
								<p>Kod pocztowy: {{ shippingZipCode }}</p>
								<p>Miasto: {{ shippingCity }}</p>
								<p>Numer telefonu: {{ shippingTelephonNumber }}</p>
								<p>Email: {{ shippingEmail }}</p>
							</div>
						</div>
						<div
							class="buttons-container"
							:class="{addMoreSpaceToButtonsContainer: shippingMethod === 'wysyłka'}"
						>
							<button
								class="btn btn-primary"
								type="button"
								@click="handleConfirmInfoButton('stepSix')"
								>Zatwierdź</button
							><button
								class="btn btn-outline-primary"
								type="button"
								@click="handleEditMode(6)"
								>Edytuj</button
							>
						</div>
					</div>
				</div>
				<div class="step-container" v-show="stepPage === 8">
					<div class="step-control-panel summary">
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Wybór miejsca nadruku: {{ imagePosition }}</p
							>
						</div>
					</div>
					<div class="step-control-panel summary">
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Wybór grafiki:
							</p>
							<div class="draw-image-container">
								<img :src="imgUrl" alt="t-shirt logo" />
							</div>
						</div>
					</div>
					<div class="step-control-panel summary" style="border-bottom: 1px solid black;">
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Wybór stylu grafiki: {{ imageStyle }}
								{{ imageStyle === 'rozmycie' ? blurValue : '' }}</p
							>
						</div>
					</div>
					<div
						class="step-control-panel summary"
						style="border-bottom: 1px solid black; margin-top: 10px;"
					>
						<div class="step-information-content">
							<p style="font-weight: bold;" class="step-information-text"
								>Dane klienta:
							</p>
							<p>Imię: {{ firstName }}</p>
							<p>Nazwisko: {{ lastName }}</p>
							<p>Ulica: {{ street }}</p>
							<p>Numer budynku: {{ buildingNumber }}</p>
							<p>Numer mieszkania: {{ apartmentNumber }}</p>
							<p>Kod pocztowy: {{ zipCode }}</p>
							<p>Miasto: {{ city }}</p>
							<p>Numer telefonu: {{ telephonNumber }}</p>
							<p>Email: {{ email }}</p>
						</div>
					</div>
					<div
						class="step-control-panel summary"
						style="border-bottom: 1px solid black; margin-top: 10px;"
					>
						<div class="step-information-content">
							<p style="font-weight: bold;">Sposób odbioru: {{ shippingMethod }}</p>
							<div
								class="shipping-information-content"
								v-if="shippingMethod === 'wysyłka'"
							>
								<p style="font-weight: bold;" class="step-information-text"
									>Dane odbiorcy:
								</p>
								<p>Imię: {{ shippingFirstName }}</p>
								<p>Nazwisko: {{ shippingLastName }}</p>
								<p>Ulica: {{ shippingStreet }}</p>
								<p>Numer budynku: {{ shippingBuildingNumber }}</p>
								<p>Numer mieszkania: {{ shippingApartmentNumber }}</p>
								<p>Kod pocztowy: {{ shippingZipCode }}</p>
								<p>Miasto: {{ shippingCity }}</p>
								<p>Numer telefonu: {{ shippingTelephonNumber }}</p>
								<p>Email: {{ shippingEmail }}</p>
							</div>
						</div>
					</div>
				</div>
				<div class="hide">
					<button ref="submitButton" type="submit">Submit</button>
				</div>
			</form>
			<div key="presentationPart" class="presentation" style="background-color: transparent;">
				<div class="images-container">
					<div class="front-shirt-image-container">
						<img
							class="t-shirt-logo"
							v-if="imagePosition === 'przód' || imagePosition === 'przód i tył'"
							:src="imgUrl"
							alt="t-shirt logo front"
						/>
					</div>
					<div class="back-shirt-image-container">
						<img
							class="t-shirt-logo"
							v-if="imagePosition === 'tył' || imagePosition === 'przód i tył'"
							:src="imgUrl"
							alt="t-shirt logo back"
						/>
					</div>
				</div>
				<p style="font-weight: bold">Cena koszulki: {{ computedPrice }} $</p>
				<div class="btn-group" role="group" v-show="stepPage !== 8">
					<button
						class="btn btn-primary"
						:disabled="stepPage === 8"
						@click="handleStepBack"
						v-show="stepPage !== 1 && !isEditModeOn"
						>Wstecz</button
					>
					<button
						class="btn btn-primary"
						v-show="stepPage !== 8 && !isEditModeOn"
						:disabled="
							!confirmInfo ||
								(stepPage === 5 && isFirstFormValid) ||
								(stepPage === 6 && isSecondFormValid)
						"
						@click="submitByRef"
						>{{ buttonNextTextContent }}</button
					>
					<button
						class="btn btn-primary"
						:disabled="
							(stepPage === 5 && isFirstFormValid) ||
								(stepPage === 6 && isSecondFormValid)
						"
						v-show="isEditModeOn"
						@click="handleLeaveEditMode"
						>Zatwierdź</button
					>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import {required, email, requiredUnless} from 'vuelidate/lib/validators';
export default {
	name: 'OrderForm',
	data() {
		return {
			imagePosition: 'przód',
			imageStyle: 'normalny',
			blurValue: 1,
			firstName: '',
			lastName: '',
			street: '',
			buildingNumber: null,
			apartmentNumber: null,
			zipCode: null,
			city: '',
			telephonNumber: null,
			email: '',
			shippingMethod: 'odbiór osobisty',
			shippingFirstName: '',
			shippingLastName: '',
			shippingStreet: '',
			shippingBuildingNumber: null,
			shippingApartmentNumber: null,
			shippingZipCode: null,
			shippingCity: '',
			shippingTelephonNumber: null,
			shippingEmail: '',
			stepPage: 1,
			previousStepPage: null,
			isEditModeOn: false,
			confirmInfo: true,
			confirmInfoParts: {
				stepOne: false,
				stepTwo: false,
				stepThree: false,
				stepFive: false,
				stepSix: false,
			},
			idsArr: [],
			imgUrl: '',
			currentImgId: null,
		};
	},
	validations: {
		firstName: {
			required,
		},
		lastName: {
			required,
		},
		street: {
			required,
		},
		buildingNumber: {
			required,
		},
		zipCode: {
			required,
		},
		city: {
			required,
		},
		email: {
			required,
			email,
		},
		shippingFirstName: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingLastName: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingStreet: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingBuildingNumber: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingZipCode: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingCity: {
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingEmail: {
			email,
			required: requiredUnless((vm) => {
				vm.shippingMethod !== 'wysyłka';
			}),
		},
		shippingMethod: {
			required,
		},
	},
	methods: {
		onSubmit() {
			const formData = {
				email: this.email,
				imagePosition: this.imagePosition,
				imageStyle: this.imageStyle,
				blurValue: this.blurValue,
				firstName: this.firstName,
				lastName: this.lastName,
				street: this.street,
				buildingNumber: this.buildingNumber,
				apartmentNumber: this.apartmentNumber,
				zipCode: this.zipCode,
				city: this.city,
				telephonNumber: this.telephonNumber,
				shippingMethod: this.shippingMethod,
				shippingFirstName: this.shippingFirstName,
				shippingLastName: this.shippingLastName,
				shippingStreet: this.shippingStreet,
				shippingBuildingNumber: this.shippingBuildingNumber,
				shippingApartmentNumber: this.shippingApartmentNumber,
				shippingZipCode: this.shippingZipCode,
				shippingCity: this.shippingCity,
				shippingTelephonNumber: this.shippingTelephonNumber,
				shippingEmail: this.shippingEmail,
				price: this.computedPrice,
				imageUrl: this.imgUrl,
			};
			console.log(formData);
		},
		submitByRef() {
			if (this.stepPage === 7) {
				this.$refs.submitButton.click();
			}

			this.stepPage++;

			if (this.stepPage === 4) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree
				) {
					this.confirmInfo = true;
					return;
				}
				this.confirmInfo = false;
				return;
			} else if (this.stepPage === 7) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree &&
					this.confirmInfoParts.stepFive &&
					this.confirmInfoParts.stepSix
				) {
					this.confirmInfo = true;
					return;
				}
				this.confirmInfo = false;
				return;
			} else {
				this.confirmInfo = true;
			}
		},
		handleEditMode(step) {
			this.previousStepPage = this.stepPage;
			this.isEditModeOn = true;
			this.stepPage = step;
		},
		handleLeaveEditMode() {
			this.isEditModeOn = false;
			this.stepPage = this.previousStepPage;
			this.previousStepPage = null;
		},
		handleConfirmInfoButton(step) {
			this.confirmInfoParts[step] = true;

			if (this.stepPage === 4) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree
				) {
					this.confirmInfo = true;
				}
			} else if (this.stepPage === 7) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree &&
					this.confirmInfoParts.stepFive &&
					this.confirmInfoParts.stepSix
				) {
					this.confirmInfo = true;
				}
			}
		},
		handleDrawButton() {
			let id;
			const copyArr = this.idsArr.slice();

			if (this.idsArr[0] !== this.currentImgId) {
				this.idsArr[0] = copyArr[1];
			}

			this.imageStyle = 'normalny';
			this.blurValue = 1;
			axios.get('https://picsum.photos/150').then((res) => {
				let string = res.request.responseURL;
				let index = res.request.responseURL.indexOf('id');
				string = string.substring(index + 3);
				id = string.split('/')[0];
				this.idsArr[1] = id;
				this.currentImgId = id;
				this.imgUrl = `https://picsum.photos/id/${id}/150`;
			});
		},
		handlePrevImgButton() {
			if (this.idsArr[0] !== undefined) {
				this.currentImgId = this.idsArr[0];

				this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150`;
			}
		},
		handleNextImgButton() {
			this.currentImgId = this.idsArr[1];

			this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150`;
		},
		handleShippingInputs() {
			this.shippingFirstName = this.firstName;
			this.shippingLastName = this.lastName;
			this.shippingStreet = this.street;
			this.shippingBuildingNumber = this.buildingNumber;
			this.shippingApartmentNumber = this.apartmentNumber;
			this.shippingZipCode = this.zipCode;
			this.shippingCity = this.city;
			this.shippingTelephonNumber = this.telephonNumber;
			this.shippingEmail = this.email;
		},
		handleStepBack() {
			this.stepPage--;
			if (this.stepPage === 4) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree
				) {
					this.confirmInfo = true;
					return;
				}
				this.confirmInfo = false;
				return;
			} else if (this.stepPage === 7) {
				if (
					this.confirmInfoParts.stepOne &&
					this.confirmInfoParts.stepTwo &&
					this.confirmInfoParts.stepThree &&
					this.confirmInfoParts.stepFive &&
					this.confirmInfoParts.stepSix
				) {
					this.confirmInfo = true;
					return;
				}
				this.confirmInfo = false;
				return;
			} else {
				this.confirmInfo = true;
			}
		},
	},
	computed: {
		computedPrice() {
			let price = 0;

			if (this.imagePosition === 'przód i tył') {
				price += 20;
			} else {
				price += 10;
			}

			if (this.imageStyle === 'skala szarości') {
				price += 2;
			} else if (this.imageStyle === 'rozmycie') {
				price += 3;
			}

			if (this.shippingMethod === 'wysyłka') {
				price += 5;
			}

			return price;
		},
		isFirstFormValid() {
			if (
				!this.$v.email.$error &&
				this.$v.email.required &&
				this.$v.firstName.required &&
				this.$v.lastName.required &&
				this.$v.street.required &&
				this.$v.buildingNumber.required &&
				this.$v.zipCode.required &&
				this.$v.city.required
			) {
				return false;
			}

			return true;
		},
		isSecondFormValid() {
			if (
				!this.$v.shippingEmail.$error &&
				this.$v.shippingEmail.required &&
				this.$v.shippingFirstName.required &&
				this.$v.shippingLastName.required &&
				this.$v.shippingStreet.required &&
				this.$v.shippingBuildingNumber.required &&
				this.$v.shippingZipCode.required &&
				this.$v.shippingCity.required
			) {
				return false;
			} else if (this.shippingMethod !== 'wysyłka') {
				return false;
			}

			return true;
		},
		buttonNextTextContent() {
			if (this.stepPage === 4) {
				return 'Do kasy';
			} else if (this.stepPage === 7) {
				return 'Złóż zamówienie';
			}
			return 'Dalej';
		},
	},
	watch: {
		imageStyle() {
			if (this.imageStyle === 'skala szarości') {
				this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150?grayscale`;
			} else if (this.imageStyle === 'rozmycie') {
				this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150?blur=${this.blurValue}`;
			} else {
				this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150`;
			}
		},
		blurValue() {
			this.imgUrl = `https://picsum.photos/id/${this.currentImgId}/150?blur=${this.blurValue}`;
		},
		shippingMethod() {
			if (this.shippingMethod === 'odbiór osobisty') {
				this.shippingFirstName = '';
				this.shippingLastName = '';
				this.shippingStreet = '';
				this.shippingBuildingNumber = null;
				this.shippingApartmentNumber = null;
				this.shippingZipCode = null;
				this.shippingCity = '';
				this.shippingTelephonNumber = null;
				this.shippingEmail = '';
			}
		},
	},
	beforeMount() {
		let id;
		axios.get('https://picsum.photos/150').then((res) => {
			let string = res.request.responseURL;
			let index = res.request.responseURL.indexOf('id');
			string = string.substring(index + 3);
			id = string.split('/')[0];
			this.idsArr[1] = id;
			this.currentImgId = id;
			this.imgUrl = `https://picsum.photos/id/${id}/150`;
		});
	},
};
</script>

<style scoped>
.signup-form {
	width: 400px;
	margin: 30px auto;
	border: 1px solid #eee;
	padding: 20px;
	box-shadow: 0 2px 3px #ccc;
}

.input {
	margin: 10px auto;
	min-width: 80%;
}

.input label {
	display: block;
	color: #4e4e4e;
	margin-bottom: 6px;
}

.input input {
	font: inherit;
	width: 100%;
	padding: 6px 12px;
	box-sizing: border-box;
	border: 1px solid #ccc;
}

.input input:focus {
	outline: none;
	border: 1px solid #521751;
	background-color: #eee;
}

.input.invalid label {
	color: red;
}

.input.invalid input {
	border: 1px solid red;
	background-color: #ffc9aa;
}

.input select {
	border: 1px solid #ccc;
	font: inherit;
}

.progress-bar {
	display: flex;
	flex-direction: row;
	margin: 10px 0;
	user-select: none;
}
.progress-bar .step {
	text-align: center;
	width: 100%;
	position: relative;
}
.progress-bar .step p {
	font-weight: 500;
	font-size: 18px;
	color: #000;
	margin-bottom: 8px;
}

.progress-bar .step .bullet {
	height: 25px;
	width: 25px;
	border: 2px solid #000;
	display: inline-block;
	border-radius: 50%;
	position: relative;
	transition: 0.2s;
	font-weight: 500;
	font-size: 17px;
	line-height: 25px;
}
.progress-bar .step .bullet.step-done {
	border-color: #d43f8d;
	background: #d43f8d;
}
.progress-bar .step .bullet span {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	z-index: 1000;
}

.progress-bar .step .bullet:before,
.progress-bar .step .bullet:after {
	position: absolute;
	content: '';
	bottom: 11px;
	right: -44px;
	height: 3px;
	width: 44px;
	background: #262626;
	z-index: -10;
}
.progress-bar .step .bullet.step-done:after {
	background: #d43f8d;
	transform: scaleX(0);
	transform-origin: left;
	animation: animate 0.3s linear forwards;
}

@keyframes animate {
	100% {
		transform: scaleX(1);
	}
}

.progress-bar .step:last-child .bullet:before,
.progress-bar .step:last-child .bullet:after {
	display: none;
}

.step-container {
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
}

.draw-image-container {
	height: 150px;
	width: 150px;
	margin-bottom: 10px;
	border: 1px solid white;
}

.images-container {
	display: flex;
	flex-direction: column;
	height: 400px;
	width: 200px;
	margin: 10px auto;
}

.images-container .image-container {
	border: 1px solid black;
	width: 100%;
}

.step-control-panel {
	display: flex;
	flex-direction: row;
	width: 100%;
}

.step-information-content {
	display: flex;
	flex-direction: column;
	align-items: center;
	width: 74%;
}

.radios-shipping-container {
	display: flex;
	flex-direction: column;
	align-items: flex-start;
	width: 100%;
}

.shipping-information-content {
	display: flex;
	flex-direction: column;
	width: 100%;
}

.summary {
	width: 100%;
	align-items: center;
	justify-content: center;
}

.hide {
	display: none;
}

.front-shirt-image-container {
	position: relative;
	background-image: url('../assets/t-shirt1.jpg');
	width: 200px;
	height: 200px;
	background-position: center;
	background-size: cover;
}

.back-shirt-image-container {
	position: relative;
	background-image: url('../assets/t-shirt2.jpg');
	width: 200px;
	height: 200px;
	background-position: center;
	background-size: cover;
}

.t-shirt-logo {
	width: 75px;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
}

.confirmed-field {
	background-color: #00ff0033;
}

.buttons-container button {
	width: 80%;
	margin-top: 5px;
	margin-bottom: 5px;
}

.bullet {
	background-color: #fff;
}

.bullet span {
	color: black;
}

.step-done {
	background-color: #007bff;
}

.step-done span {
	color: white;
}

.addMoreSpaceToButtonsContainer {
	padding-top: 150px;
}

.addMoreSpaceToShippingSummary {
	margin-top: 30px;
}
</style>
