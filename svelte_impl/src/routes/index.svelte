<script lang="ts">
    let username = "unknown";
    let commands: any[] = [];
    
    let initial = true;

    let currentCommand = "";

    const handleCommands = () => {
        if (!currentCommand.length) {
            return;
        }
        const targetCommand: any | undefined = allCommands.find((cmd) => currentCommand.toLowerCase().startsWith(cmd.name));
        commands = [...commands, {inputted: true, content: currentCommand},
                    {inputted: false, content: targetCommand ?
                                               targetCommand.callback() : `Command not found: ${currentCommand}`}];
        currentCommand = "";
    }

    const getPreviousCommand = (event: any) => {
        if (event.code === "ArrowUp") {
            const previousCommand = commands.filter((cmd) => cmd.inputted)
            if (previousCommand) {
                currentCommand = previousCommand.reverse()[0].content;
            }
        }
    }

    const setUsernameCmd = () => {
        const newUsername = currentCommand.split(" ")[1];

        if (!newUsername) {
            return `Invalid username: ${newUsername || ""}`;
        }

        username = newUsername
        currentCommand = "";
        return `Username set to ${username}`;
    }

    const clearCmd = () => {
        commands = [];
        currentCommand = "";
        return "";
    }
    
    const echoCmd = () => {
        const echo = currentCommand.split(" ");
        currentCommand = "";
        return `${echo.slice(1)?.join(" ") || ""}`;
    }

    const allCommands = [
        {
            name: "set-username",
            callback: setUsernameCmd
        },
        {
            name: "clear",
            callback: clearCmd
        },
        {
            name: "echo",
            callback: echoCmd
        },
    ]
</script>

<main>

{#if initial}
<h5 class="py-3">Bash (idk what to put here)</h5>
{/if}

{#each commands as command (command)}
<div class="flex">
    {#if command.inputted}
    <p class="pr-3">{username}@Terminal-Emulator-Svelte ~ %</p>
    {/if}
    <p class="pb-3">{command.content}</p>    
</div>
    
{/each}

<div class="flex">
<p class="pr-3">{username}@Terminal-Emulator-Svelte ~ %</p>

<form on:submit|preventDefault={handleCommands}>
    <input type="text" bind:value={currentCommand} on:keyup={getPreviousCommand} autofocus>
</form>

</div>
</main>

<style lang="postcss">
input[type=text] {
    @apply bg-black caret-white focus:outline-none;
}
</style>