<script>
    import { page } from '$app/stores';
    import ListItem from '$lib/components/ListItem.svelte';
    let { data } = $props();
    let currentQuestion = $state(0);
    let selectedAnswer = $state('');
    let score = $state(0);
    let submitted = $state(false);
    let errorMessage = $state('');  // Error message state

    function handleSubmit() {
        if (!selectedAnswer) {
            errorMessage = 'Please select an answer before submitting!';  // Set error message if no answer is selected
            return;
        }

        data.questions[currentQuestion].selectedAnswer = selectedAnswer;
        submitted = true;
        errorMessage = '';  // Clear error message if an answer is selected
        if (selectedAnswer === data.questions[currentQuestion].answer) {
            score++;
        }
    }
    
    function nextQuestion() {
        currentQuestion++;
        submitted = false;
        selectedAnswer = "";
        errorMessage = '';  // Clear any error when moving to the next question
    }
</script>

<style>
    /* Center container for better layout */
    .quiz-container {
        max-width: 600px;
        margin: auto;
        padding: 20px;
        background-color: #f9f9fb;
        border-radius: 10px;
        box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.1);
    }

    /* Progress bar styling */
    .progress {
        width: 100%;
        height: 20px;
        border-radius: 10px;
        overflow: hidden;
        accent-color: #6b46c1;
        background-color: #f3f3f3;
    }
    .progress::-webkit-progress-value {
        background-color: #6b46c1;
        transition: width 0.5s ease;
    }
    .progress::-moz-progress-bar {
        background-color: #6b46c1;
        transition: width 0.5s ease;
    }

    /* Button styling */
    .btn-primary {
        background-color: #6b46c1;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s, transform 0.2s;
    }
    .btn-primary:hover {
        background-color: #583d9a;
        transform: scale(1.05);
    }
    .btn-primary:disabled {
        background-color: #c0a9e3;
        cursor: not-allowed;
    }

    /* Title and text styling */
    h2 {
        color: #333;
        font-size: 1.5em;
        margin-bottom: 10px;
    }
    p {
        color: #555;
    }

    /* Error message styling */
    .error-message {
        color: red;
        font-size: 1.1em;
        margin-top: 10px;
        font-weight: bold;
    }

    /* Result styling */
    .result {
        text-align: center;
        font-size: 1.2em;
        font-weight: bold;
        color: #6b46c1;
    }
</style>

<div class="quiz-container">
    {#if currentQuestion < data.questions.length}

        {#each data.questions as question, index}
            {#if index === currentQuestion}
            <p>Question {index + 1} of {data.questions.length}</p>
            <h2>{question.question}</h2>
                {#each question.options as option, index(option)}
                {#key submitted}
                    <ListItem 
                        title={option} 
                        {index} 
                        correctAnswer={question.selectedAnswer && question.answer === option} 
                        incorrectlySelected={question.selectedAnswer === option && question.answer !== option}
                        focusAction={() => (selectedAnswer = option)}
                        disabled={submitted} 
                    />
                {/key}
                {/each} 
            {/if}
        {/each}

        
        <progress class="progress w-56" value={(currentQuestion / data.questions.length) * 100} max="100"></progress>

        {#if errorMessage}
            <p class="error-message">{errorMessage}</p> <!-- Show error message if set -->
        {/if}

        {#if !submitted}
            <button class="btn btn-primary" onclick={handleSubmit} disabled={!selectedAnswer}>Submit</button>
        {:else}
            <button class="btn btn-primary" onclick={nextQuestion}>Next Question</button>
        {/if}

    {:else}
        <h2 class="result">Game OVER!</h2>
        <p class="result">You scored {score} out of {data.questions.length} points!</p>
        <button class="btn btn-primary" onclick={() => window.location.reload()}>Play again!</button>
    {/if}
</div>
