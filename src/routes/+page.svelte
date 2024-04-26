<script lang="ts">
  let okCz = false 
  let okPrefix = true
  let okAccount = true 
  let czPrefix: number | null = null
  let czAccount: number | null = null
  let euIban: string = ''
  let okIban = false 
  function changeHandlerCZ( event: Event ) {
    okPrefix = checkCZ( czPrefix )
    okAccount = checkCZ ( czAccount )
    okCz = okPrefix && okAccount
  }
  function checkCZ( acc : number | null ) : boolean {
    if( acc == null ) return true ;
    let x = 0;
    let weight = 1;
    const modul = 11; 
    while(acc > 0) {
      x += (acc % 10) * weight;
      acc = (acc - (acc % 10)) / 10;
      weight = weight * 2;
    }
    return (x % modul == 0)
  }

  function changeHandlerIban( event: Event ) {
    okIban = checkIban( euIban )
  }

  function checkIban( iban : string ) : boolean {
    const cA = 'A'.charCodeAt(0) ;
    const c0 = '0'.charCodeAt(0)
    iban = iban.replace(/\s+/g, '')
    iban = iban.slice(4).toUpperCase() + iban.slice(0,4).toUpperCase()
    let converted : bigint = 0n  
    for( let i=0; i < iban.length ; i++ ) {
      let x = 0n ;
      if( iban[i] >= 'A' && iban[i] <= 'Z' ) {
        x = BigInt(iban.charCodeAt(i) - cA + 10) ;
        converted = converted * 100n + x 
      }
      else if ( iban[i] >= '0' && iban[i] <= '9') {
        x = BigInt( iban.charCodeAt(i) - c0 );
        converted = converted * 10n + x 
      };
    }
    console.log( converted, converted % 97n, converted % 98n)
    return (converted % 97n == 1n )
  }

</script>
<div class="x3">
  <label for="prefix">Předčíslí</label><label for="czaccount">Číslo účtu</label>
  <!-- svelte-ignore a11y-label-has-associated-control -->
  <label>Kontrola</label>
  
  <input type=number id="prefix" bind:value={czPrefix} on:change={changeHandlerCZ} class:green={okPrefix} class:red={!okPrefix}>
  <input type=number id="czaccount" bind:value={czAccount}  on:change={changeHandlerCZ} class:green={okAccount} class:red={!okAccount}>

  <!-- svelte-ignore a11y-label-has-associated-control -->
  <label class:green={okCz} >{okCz ? "OK" : "Chyba"}</label>
  
</div>

<div class="x2">
  <label for="iban">IBAN</label>
  <!-- svelte-ignore a11y-label-has-associated-control -->
  <label>Kontrola</label>
  
  <input type=text id="iban" size=48 bind:value={euIban} on:change={changeHandlerIban} class:green={okIban} class:red={!okIban}>
  
  <!-- svelte-ignore a11y-label-has-associated-control -->
  <label class:green={okIban} >{okIban ? "OK" : "Chyba"}</label>
  
</div>

<style>
  .green { border-color: green; background-color: palegreen; }
  .red { border-color: red; border-width: 3px ; color: red }
  .x3 { display: grid ; grid-template-columns: min-content min-content min-content; gap: 3px }
  .x2 { display: grid ; grid-template-columns: min-content min-content; gap: 3px }
  .x2 input { width: 50 ex }
</style>
