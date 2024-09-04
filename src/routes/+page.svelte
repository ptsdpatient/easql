<script>
    import {onMount} from 'svelte'
    import Prism from 'prismjs';
    import 'prismjs/components/prism-sql.js'; 
    import './prism-dark.css'
    import {DataType} from './sql_datatypes.ts'

    let tableMode=true,nullable=false,unique=false,foreign=false;

    let nameValue="";
    let data_type=DataType.INT;

    let columns =[]
    let tables=[
    
    ]




    function insertColumn(){

        tables.forEach(table => {
            if(table.active){
                    table.columns=[
                        ...table.columns,{
                            name:nameValue,
                            active:true,
                            data_type:data_type,
                            nullable:nullable,
                            unique:unique,
                            foreign:foreign,
                        }
                    ]
            }
        })
        clearValues();
    }



    function deactivateTables(){
        tables.forEach(table => {table.active=false;})
    }

    function insertTable(){
        
        if(tableMode&&tables.length<0)return false;

        deactivateTables();

        tables=[
            ...tables,{
                name:nameValue,
                active:true,
            }
        ]
        clearValues();
    }

    function clearValues(){
        nameValue="";
        data_type=DataType.INT;
        nullable=false;
        unique=false;
        foreign=false;
    }

    onMount(() => {
        Prism.highlightAll();
    });
</script>

<div class="bg-gray-800" style="height:100svh;width:100vw;">
    <div class="w-full h-full flex flex-row">
        <div class="w-1/2 h-full flex flex-col text-base">
            <div class="h-1/2 w-full flex flex-col gap-3 text-white justify-around py-5 accent-blue-400">

                <div class="flex flex-row items-center  w-full justify-around">

                    <button class="flex flex-row items-center gap-3" on:click={() => tableMode = true}>
                        <input type="radio" checked={tableMode === true} readOnly>
                        <div>Create Table</div>
                      </button>
                      
                      <button class="flex flex-row items-center gap-3" on:click={() => tableMode = false}>
                        <input type="radio" checked={tableMode === false} readOnly >
                        <div>Insert Column</div>
                      </button>
                </div>

                <div class="flex flex-row gap-4 justify-around px-5 gap-3 text-white">
                    <input type="text" class="bg-gray-700 w-2/3 px-5 py-2 " bind:value={nameValue} placeholder={tableMode?"Table Name":"Column Name"} required>
               
                    <button class="flex flex-row items-center gap-3 bg-gray-700 {tableMode?"text-gray-500 bg-opacity-70":""} w-1/3 px-5 py-2" on:click={() => nullable = !nullable}>
                        <input type="radio" checked={nullable === true} readOnly disabled={tableMode} >
                        <div>is nullable?</div>
                    </button>
                 
                </div>
                
                <div class="flex flex-row gap-4 justify-around px-5 gap-3 text-white" >
                    <select class="bg-gray-700 w-2/3 px-5 py-2 {tableMode?"text-gray-500 ":""}" disabled={tableMode}>
                        <option value="">Select Data Type</option>
                        {#each DataType as dataType}
                            <option value="{dataType.data_type}">{dataType.name}</option>
                        {/each}
                    </select>

                    <button class="flex flex-row items-center gap-3 bg-gray-700 w-1/3 px-5 py-2 {tableMode?"text-gray-500 bg-opacity-70":""}" on:click={() => unique = !unique}>
                        <input type="radio" checked={unique === true} readOnly disabled={tableMode}>
                        <div>is unique?</div>
                    </button>
                </div>

                <div class="flex flex-row gap-4 justify-around px-5 text-white">
                    <button class="flex flex-row items-center gap-3 bg-gray-700 w-1/3 px-5 py-2 {tableMode?"text-gray-500  bg-opacity-70":""}" on:click={() => foreign = !foreign}>
                        <input type="radio" checked={foreign === true} readOnly disabled={tableMode}>
                        <div>is foreign?</div>
                    </button>
                    <select class="bg-gray-700 w-2/3 px-5 py-2 {tableMode?"text-gray-500":""}" disabled={tableMode}>
                        {#each columns as column}
                            <option value="{column}">{column}</option>
                        {/each}
                    </select>
                </div>
                
                <div class="flex flex-row w-full justify-around text-white">                   
                    <button class="bg-red-600 px-10 py-2 rounded-lg" on:click={clearValues}>
                        <div>Clear</div>
                    </button>
                    <button class="bg-blue-500 px-10 py-2 rounded-lg" on:click={()=>{tableMode?insertTable():insertColumn()}}>
                        <div>Insert</div>
                    </button>
                </div>

            </div>


            <div class="h-1/2 w-full flex flex-row p-4 gap-3">

                <div class="w-1/3 h-full flex flex-col bg-gray-700 rounded-lg text-white items-center p-2 overflow-y-auto">
                    <div>Tables </div>
                    {#each tables as table}
                        <button class="text-left w-full pl-3 {table.active?"bg-gray-500":"hover:bg-gray-600"} hover:bg-opacity-80 rounded-lg transform transition-all duration-300 hover:scale-95" on:click={()=>{deactivateTables();table.active=true;}}>{table.name}</button>
                    {/each}
                </div>

                <div class="w-2/3 h-full flex flex-col bg-gray-700 rounded-lg text-white p-2 overflow-y-auto">
                    <div>Columns </div>
                        {#each tables as table}
                            <button class="text-left w-full pl-5 {table.active?"bg-gray-500":"hover:bg-gray-600"} hover:bg-opacity-80 rounded-lg transform transition-all duration-300 hover:scale-95" on:click={()=>{deactivateTables();table.active=true;}}>{table.name}</button>
                            {#each table.columns as column}
                                <button class="text-left w-2/3 pl-5 ml-10 {column.active?"bg-gray-500":"hover:bg-gray-600"} hover:bg-opacity-80 rounded-lg transform transition-all duration-300 hover:scale-95" on:click={()=>{deactivateTables();column.active=true;}}>{column.name}</button>
                            {/each}
                        {/each}
                </div>

            </div>

        </div>
        <div class="w-1/2 h-full bg-gray-800 px-4 py-4">
            <div class="w-full h-full flex flex-col">
                <div class="flex flex-row justify-end">
                    <button class="bg-indigo-600 px-10 py-2  text-white">
                        <div>Copy</div>
                    </button>
                    <button class="bg-yellow-600 px-10 py-2  text-white">
                        <div>Export</div>
                    </button>
                </div>
                <div class="overflow-y-auto bg-gray-900 w-full h-full">
                    <pre>
                        <code class="language-sql">
    CREATE TABLE table_name (
        column1 datatype,
        column2 datatype,
        column3 datatype,
    ....
    );
                        </code>
                    </pre>
                </div>
            </div>
        </div>
    </div>
</div>



<style>
    @font-face{
        font-family: "poppins";
        src:url('/poppins.ttf');
    }
   
</style>