<script setup>
    import { ref, onMounted } from 'vue'
    const firstNumber = ref(parseInt(localStorage.getItem('firstNumber')) || 0);
    const lastNumber = ref(parseInt(localStorage.getItem('lastNumber')) || 1);
    const borderColor = ref(localStorage.getItem('borderColor') || '#ffffff');
    const innerColor = ref(localStorage.getItem('innerColor') || '#808080');
    const generate = ref(localStorage.getItem('generate') === 'true' || false);
    const clickedSquares = ref(JSON.parse(localStorage.getItem('clickedSquares')) || []);

    function generateGrid(first, last) {
        if(!isNaN(first) && !isNaN(last)){
            if(last > first && last <= 1000 && first >= 0 && last - first > 0){
                generate.value = true;
                localStorage.setItem('firstNumber', first);
                localStorage.setItem('lastNumber', last);
                localStorage.setItem('generate', true);
                localStorage.setItem([]);
            }
        }
    }

    function reset() {
        generate.value = false;
        clickedSquares.value = [];
        borderColor.value = '#ffffff';
        innerColor.value = '#808080';
        firstNumber.value = 0;
        lastNumber.value = 1;

        // Rimuovi tutti i valori dal localStorage
        localStorage.removeItem('firstNumber');
        localStorage.removeItem('lastNumber');
        localStorage.removeItem('borderColor');
        localStorage.removeItem('innerColor');
        localStorage.removeItem('generate');
        localStorage.removeItem('clickedSquares');
    }

    function toggleClass(elementId) {
        const index = clickedSquares.value.indexOf(elementId);
        const element = document.getElementById(elementId);

        if (index === -1) {
            clickedSquares.value.push(elementId);
            localStorage.setItem('clickedSquares', JSON.stringify(clickedSquares.value));
            element.classList.add('checked');
        } else {
            clickedSquares.value.splice(index, 1);
            localStorage.setItem('clickedSquares', JSON.stringify(clickedSquares.value));
            element.classList.remove('checked');
        }
    }

    // Funzione per aggiornare il colore selezionato
    const updateColor = (color) => {
        innerColor.value = color;
        localStorage.setItem('innerColor', color);
        updateBorderColor();
    };

    // Funzione per aggiornare il colore del bordo
    const updateBorderColor = () => {
        document.documentElement.style.setProperty('--border-color', innerColor.value);
        localStorage.setItem('borderColor', innerColor.value);
    };

    // Aggiorna il colore del bordo all'avvio
    updateBorderColor();

    //ricarica i riquadri cliccati all'avvio
    onMounted(() => {
        // Aggiungi le classi 'checked' ai quadrati cliccati
        clickedSquares.value.forEach(id => {
            const element = document.getElementById(id);
            if (element) {
                element.classList.add('checked');
            }
        });
    });
</script>




<template>
    <div class="container-fluid mt-5">
        <div class="d-flex justify-content-center pt-4 align-items-center">
            <div class="d-flex flex-column gap-5" v-if="!generate">
                <div class="d-flex flex-column gap-5">
                    
                    <div class="d-flex gap-5 justify-content-center">
                        <div class="d-flex flex-column gap-2 align-items-start">
                            <label for="firstNumber" class="align-self-center">Select first number</label>
                            <input type="number" min="0" :max="lastNumber - 1" step="1"  v-model="firstNumber" id="firstNumber" class="inputWidth"/>
                        </div>
                        
                        <div class="d-flex flex-column gap-2 align-items-start">
                            <label for="lastNumber" class="align-self-center">Select last number</label>
                            <input type="number" :min="0 + firstNumber" max="1000" step="1"  v-model="lastNumber" id="lastNumber" class="inputWidth"/>
                        </div>
                    </div>

                    <div class="d-flex gap-5 justify-content-center">
                        <div class="d-flex flex-column gap-2 align-items-center">
                            <label for="firstNumber">Grid color</label>
                            <input type="color" id="colorPicker" name="colorPicker" v-model="borderColor">
                        </div>
    
                        <div class="d-flex flex-column gap-2 align-items-center">
                            <label for="firstNumber">Inner color</label>
                            <input type="color" id="colorPicker" name="colorPicker" @change="updateColor($event.target.value)">
                        </div>
                    </div>
                </div>
                
                <div v-if="!generate" class="align-self-center">
                    <button class="btn btn-success" @click="generateGrid(firstNumber, lastNumber)">Generate!</button>
                </div>
            </div>
    
            <div v-else>
                <button class="btn btn-warning" @click="reset()">Reset</button>
            </div>
    
        </div>
        
        <div class="row justify-content-center"  v-if="generate">
            <div class="d-flex flex-wrap gap-4 pt-5 col-sm-12 col-md-11 justify-content-center">
                <div :class="['square', {clicked: isClicked}]" v-for="i in lastNumber - firstNumber + 1" :key="i" :id="i" @click="toggleClass(i)" :style="{ borderColor: borderColor }">
                    {{ firstNumber -1 + i }}
                </div>
            </div>
            <h2 class="mt-5 text-center">Left click to check a box</h2>
        </div>
    </div>
    
</template>




<style scoped>
    :root{
        --border-color: #ffffff;
    }

    .square{
        border: 1px solid;
        padding: 10px;
        display: flex;
        align-items: center;
        justify-content: center;
        aspect-ratio: 1;
        --gap: 1.5rem;
        --columns: 20;
        flex-basis: calc((100% / var(--columns)) - var(--gap) + (var(--gap) / var(--columns)));
    }

    .checked{
        background-color: var(--border-color);
        color: var(--border-color);
    }

    .inputWidth{
        width: 150px;
    }

    @media screen and (max-width: 319px) {
        .square{
            --columns: 1;
        }
    }

    @media screen and (min-width: 320px) {
        .square{
            --columns: 4;
        }
    }

    @media screen and (min-width: 576px) {
        .square{
            --columns: 9;
        }
    }

    @media screen and (min-width: 768px) {
        .square{
            --columns: 10;
        }
    }

    @media screen and (min-width: 992px) {
        .square{
            --columns: 13;
        }
    }

    @media screen and (min-width: 1200px) {
        .square{
            --columns: 15;
        }
    }

    @media screen and (min-width: 1500px) {
        .square{
            --columns: 20;
        }
    }
</style>
