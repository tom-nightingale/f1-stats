<script context="module">
    import { Client } from '../..//gql/client';
    import { GET_DRIVER_BY_ID, GET_DRIVER_YEARS } from '../..//gql/queries';

    export async function load({ params }) {
        const d = new Date();
        const current = Number(d.getFullYear());
        const queryVariables = {
            driverId: String(params.slug),
            year: current,
        };
        const driver = await Client.request(GET_DRIVER_BY_ID, queryVariables);

        /* loop through years and query whether the driver appears */
        const startYear = 2005 //earliest our API goes
        const date = new Date();
        const currentYear = date.getFullYear();
        
        /* Create an empty array that will hold our results */
        let driverAppearsIn = [];
        for(let i = startYear; i <= currentYear; i++) {
            let query = {
                year: i,
            }
            const driverData = await Client.request(GET_DRIVER_YEARS, query);

            driverData.drivers.forEach((driver, index) => {
                if(driver.driverId === String(params.slug)) {
                    driverAppearsIn = [...driverAppearsIn, i];
                }
            })
        }

        return {
            props: {
                driver: driver.driver,
                driverYears: driverAppearsIn,
            }
        }
    }
</script>

<script lang="ts">
    import type { Driver } from '../../gql/types';
    import PageHeading from '../../components/PageHeading/index.svelte';
    import YearSelector from '../../components/YearSelector/index.svelte';
    import Title from '../../components/PageHeading/Title/index.svelte';
    import Subtitle from '../../components/PageHeading/Subtitle/index.svelte';
    export let driver:Driver;
    export let driverYears:Array<number>;
    let latestYear = driverYears.slice(-1);
</script>

<svelte:head>
    <title>{driver.givenName} {driver.familyName} | {driver.getConstructor.name} F1 Driver</title>
</svelte:head>

<PageHeading>
    <div>
        <Title title={`${driver.givenName} ${driver.familyName}`} />
        <Subtitle subtitle={`${driver.getConstructor.name}`} />
    </div>
    <YearSelector minYear={driverYears[0]} season={Number(latestYear)} slugPrefix={`/drivers`} id={driver.driverId} />
</PageHeading>

<ul>
    <li>{driver.driverId}</li>
    <li>{driver.code}</li>
    <li>{driver.url}</li>
    <li>{driver.givenName}</li>
    <li>{driver.familyName}</li>
    <li>{driver.dateOfBirth}</li>
    <li>{driver.nationality}</li>
</ul>