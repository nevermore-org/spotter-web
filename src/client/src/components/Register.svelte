<script lang="ts">
    import Textfield, {Input, InputComponentDev} from '@smui/textfield';
    import Button, { Label } from "@smui/button";
    import Icon from '@smui/textfield/icon';
    import PasswordField from './PasswordField.svelte';
    let username = "";
    let password = "";
    let passwordCheck = "";

    const sayHello = () => {
        console.log(password, passwordCheck, password === passwordCheck);
    }

</script>


<div class="input-block">
    <h1>Sign up</h1>
    <Textfield variant="outlined" style="width: 15em" bind:value={username} label="Email">
        <Icon class="material-icons" slot="leadingIcon">email</Icon>
    </Textfield>

    <PasswordField bind:password={password} label="Password" icon="lock_open"></PasswordField>
    <PasswordField bind:password={passwordCheck} label="Confirm password" icon="lock"></PasswordField>

    <Button on:click={sayHello} variant="raised">
        <Label>Sign Up</Label>
    </Button>
</div>

<style>
    h1 {
        padding-top: 0px;
        text-transform: none;
    }

    .input-block {
		display: flex;
		padding-bottom: 40px;
		justify-content: center;
		align-items: center;
        flex-direction: column;
        gap: 2em;
	}
</style>