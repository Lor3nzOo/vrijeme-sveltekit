<script>
    import {onMount} from "svelte";
    import axios from 'axios'
    import {slide} from 'svelte/transition'

    let loading = true

    let day = ''

    let data = ''

    let time = ''

    onMount(() => {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(getCoords)
        } else {
            alert('Not supported')
        }
    })

    const getCoords = async (position) => {
        const {latitude, longitude} = position.coords
        try {
            const res = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=909b25942a91dd2c10a0a46a8e77451a&lang=hr&units=metric`)
            data = res.data
            timeValue()
            setInterval(timeValue, 1000)
            switch (new Date().getDay()) {
                case 0:
                    day = "Sunday";
                    break;
                case 1:
                    day = "Monday";
                    break;
                case 2:
                    day = "Tuesday";
                    break;
                case 3:
                    day = "Wednesday";
                    break;
                case 4:
                    day = "Thursday";
                    break;
                case 5:
                    day = "Friday";
                    break;
                case 6:
                    day = "Saturday";
                    break;
            }
            loading = false
        } catch (e) {
            console.log(e)
            loading = false
        }
    }

    const timeValue = () => {
        let today = new Date()
        if (today.getMinutes() < 10) {
            time = `${today.getHours()}:0${today.getMinutes()}`
        } else {
            time = `${today.getHours()}:${today.getMinutes()}`
        }
    }

</script>

<div class="h-screen flex flex-col items-center justify-center mx-auto bg-[url('bg.jpg')]">
    {#if loading}
        <div class="loadingio-spinner-rolling-dvf2ugegcdb">
            <div class="ldio-yjw3p56rlh9">
                <div></div>
            </div>
        </div>
    {/if}
    {#if data}
        <div transition:slide="{{ y: 200, duration: 600 }}" class="flex text-white backdrop-blur-md rounded-xl p-10 gap-5 flex-col justify-center items-center mx-auto max-w-3xl md:max-w-7xl">
            <div class="flex flex-col md:flex-row justify-between items-start gap-20 md:gap-96">
                <div class="flex flex-col justify-center items-start gap-4" >
                    <h1 class="text-4xl">{time}</h1>
                    <h1 class="text-4xl">{day}</h1>
                <div class="flex flex-col justify-center items-start gap-2 text-xl">
                    <h2>Humidity: {data.main.humidity}%</h2>
                    <h2>Pressure: {data.main.pressure}hPa</h2>
                    <h2>Wind Speed: {(data.wind.speed*3.6).toFixed(2)}km/h</h2>
                </div>
                </div>
                    <h1 class="text-4xl">{data.name}</h1>
                    <h1>{data.sys.country}</h1>
                </div>
                <div style="background-image: linear-gradient(to right, rgba(0,0,0,0.1) 0%, rgba(0,0,0,0.1) 100%);" class="flex justify-center items-center py-10 rounded-lg w-full">
                    <h1 class="text-5xl">{Math.round(data.main.temp)}°</h1>
                </div>
        </div>
    {/if}
</div>
