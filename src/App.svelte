<script>
	let blocks = [];
	let blockCount = 25;
	
	function updateCount() {
		blocks = [];
		for(var i = 0; i < blockCount; i++) {
			blocks.push(0);
		}
	}
	
	$: if(blockCount) {
		updateCount();
	}
	
	let code = localStorage.getItem('code');
	
	for(var i = 0; i < blockCount; i++) {
		blocks.push(0);
	}
	
	let bi = 0;		// Block index
	let insIdx = 0;	// Instruction index
	let insJmp = 0;	// Instruction to jump to if end of loop
	let output = "";
	
	function reset() {
		for(var i = 0; i < blocks.length; i++){
			blocks[i] = 0;
		}
	}
	
	function execute() {
		console.log('Running');
		bi = 0;
		output = '';
		for(insIdx = 0; insIdx < code.length; insIdx++) {
			switch(code.charAt(insIdx)) {
				case '>':
					bi += 1;
					break;
				case '<':
					bi -= 1;
					break;
				case '+':
					blocks[bi] += 1;
					break;
				case '-':
					blocks[bi] -= 1;
					break;
				case '.':
					output += String.fromCharCode(blocks[bi]);
					console.log(String.fromCharCode(blocks[bi]));
					break;
				case ',':
					blocks[bi] = parseInt(prompt('Input Value'));
					break;
				case '[':
					insJmp = insIdx;
					break;
				case ']':
					if(blocks[bi] == 0) break;
					insIdx = insJmp;
					break;
			}
			
			for(var i = 0; i<blocks.length; i++){
				if(blocks[i] < 0) {
					blocks[i] = 255;
				}
				if(blocks[i] > 255) {
					blocks[i] = 0;
				}
			}
			
			if(bi >= blockCount) {
				bi = 0;
			}
			if(bi < 0) {
				bi = blockCount - 1;
			}
		}
	}
</script>

<h2>Brain Fuck Interpreter</h2>

<h4>Block Count</h4>
<input type="number" bind:value={blockCount} min="0">

<h4>Code</h4>
<textarea bind:value={code} on:change={() => {localStorage.setItem('code', code);}}></textarea>
<br>
<button on:click={execute}>Run</button>
<button on:click={reset} style="margin-bottom: 40px;">Reset</button>

<block>
	<span style="height: 26px; margin-right: 6px; margin-top: 2px;">Index</span>
	<span style="height: 26px;">Value</span>
</block>
<strip>
{#each blocks as block, i}
	<block style={i == bi ? 'color: red;' : ''}>
		<div>{i}</div>
		<div>{block}</div>
	</block>
{/each}
</strip>

<div style="word-wrap: break-word;">{output}</div>
<style>
	strip {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(27px, max-content));
		width: 100%;
	}
	
	block {
		display: flex;
		flex-direction: column;
		border-width: 0px;
		border-right-width: 1px;
		border-style: solid;
		border-color: black;
	}
	
	block div {
		display: flex;
		
		border-width: 1px 0px 1px 1px;
		
		min-width: 27px;
		max-width: 27px;
		width: 27px;
		height: 24px;
		
		justify-content: center;
		align-items: center;
	}
</style>
