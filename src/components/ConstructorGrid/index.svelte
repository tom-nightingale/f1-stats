<script lang="ts">
    export let year:number;
    export let data:Array<any>;

    // tidy up some of the data to grab the images from the F1 site...
    let items:Array<any> = [];
    data.forEach((item, index) => {
        let imageId:string;
        let newYear:number;
        
        switch(item.constructorId) {
            case "alfa":
                imageId = "alfa-romeo";
                newYear = 2022;
                break;
            case "red_bull":
                imageId = "red-bull-racing";
                break;
             case "toro_rosso":
                imageId = "toro-rosso";
                break;
            case "racing_point":
                imageId = "racing-point";
                break;
            case "haas":
                imageId = "haas-f1-team";
                break;
            case "aston_martin":
                imageId = "aston-martin";
                break;
            default: 
                imageId = item.constructorId;
                newYear = year;
        }

        items[index] = {
            id: item.constructorId,
            name: item.name,
            constructorYear: newYear ? newYear : year,
            imageId: imageId,
            nationality: item.nationality,
        }
    });
</script>

<div class="grid grid-cols-4 gap-4">
    {#each items as item, id (item.id)}
        <div class="p-4">
            {#if year > 2018}
            <img src={`https://www.formula1.com/content/dam/fom-website/teams/${item.constructorYear}/${item.imageId}.png.transform/4col/image.png`} alt={item.constructorName} />
            {/if}
            <h3><a sveltekit:prefetch href={`/constructors/${item.id}`}>{item.name}</a></h3>
            <h4>{item.nationality}</h4>
        </div>
    {/each}
</div>