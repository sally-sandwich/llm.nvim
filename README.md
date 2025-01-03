### llm.nvim
Welcome!

This bad boy works for these API services:
 - Ollama
 - OpenAI
 - Anthropic
 - Perplexity
 - Groq

Best way to use this is with neovim's lazy plugin manager. Here is my current config script


``` lua
return {
	{ -- Integrated LLM
		"sally-sandwich/llm.nvim",
		dependencies = { "nvim-lua/plenary.nvim" },
		config = function()
			-- Import models
			local llm = require("llm")
			local openai = require("openai")
			local anthropic = require("anthropic")
			local ollama = require("ollama")
			-- local groq = require("groq")
			-- local perplexity = require("perplexity")

			-- Import configurable params
			local my_prompts = require("custom/my_prompts")
			local my_models = require("custom/my_models")
			local constants = require("constants")

			-- Example use of models
			-- constants.models.groq = my_models.groq.mixtral_8x7b -- Use mixtral_8x7b instead of default llama3.1-70b-versatile

			-- Example use of system_prompt set up
			-- constants.prompts.system_prompt = my_prompts.helpful_prompt

			-- Example use of vars
			-- constants.vars.temp = 1.5 -- value between 0 - 2 default is 0.7, increases randomness in token sampling. Higher values create greater randomness.
			-- constants.vars.top_p = 0.5 -- value between 0 - 1 default is 1, determines the range of possible tokens to be sampled from. A value less than 1 reduces the space of possible tokens to be sampled
			-- constants.vars.presence_penalty =  -- value between -2 - 2  default is 0, a higher value increases penalty for repeating previously produced tokens

			-- Clear message buffer
			vim.keymap.set(
				{ "n", "v" },
				"<leader>zz",
				llm.reset_message_buffers,
				{ desc = "Resets LLM message buffers" }
			)
			-- Anthropic
			vim.keymap.set({ "n", "v" }, "<leader>ai", anthropic.invoke, { desc = "LLM Anthropic: Invoke" })
			vim.keymap.set({ "n", "v" }, "<leader>ac", anthropic.code, { desc = "LLM Anthropic: Code" })
			vim.keymap.set(
				{ "n", "v" },
				"<leader>ab",
				anthropic.code_all_buf,
				{ desc = "LLM Anthropic: Code entire buffer" }
			)
			vim.keymap.set({ "n", "v" }, "<leader>at", anthropic.code_chat, { desc = "LLM Anthropic: Code chat" })
			vim.keymap.set(
				{ "n", "v" },
				"<leader>aa",
				anthropic.code_chat_all_buf,
				{ desc = "LLM Anthropic: Code chat entire buffer" }
			)

			-- OpenAI
			vim.keymap.set({ "n", "v" }, "<leader>oi", openai.invoke, { desc = "LLM OpenAI: Invoke" })
			vim.keymap.set({ "n", "v" }, "<leader>oc", openai.code, { desc = "LLM OpenAI: Code" })
			vim.keymap.set({ "n", "v" }, "<leader>ob", openai.code_all_buf, { desc = "LLM OpenAI: Code entire buffer" })
			vim.keymap.set({ "n", "v" }, "<leader>ot", openai.code_chat, { desc = "LLM OpenAI: Code chat" })
			vim.keymap.set(
				{ "n", "v" },
				"<leader>oa",
				openai.code_chat_all_buf,
				{ desc = "LLM OpenAI: Code chat entire buffer" }
			)

			-- Ollama
			vim.keymap.set({ "n", "v" }, "<leader>li", ollama.invoke, { desc = "LLM Ollama: Invoke" })
			vim.keymap.set({ "n", "v" }, "<leader>lc", ollama.code, { desc = "LLM Ollama: Code" })
			vim.keymap.set({ "n", "v" }, "<leader>lb", ollama.code_all_buf, { desc = "LLM Ollama: Code entire buffer" })
			vim.keymap.set({ "n", "v" }, "<leader>lt", ollama.code_chat, { desc = "LLM OpenAI: Code chat" })
			vim.keymap.set(
				{ "n", "v" },
				"<leader>la",
				ollama.code_chat_all_buf,
				{ desc = "LLM OpenAI: Code chat entire buffer" }
			)
		end,
	},
}
```

Remember to set your api keys as system variables. Something like this in your .bashrc:

export OPENAI_API_KEY="fakeapikeyoweiuyrhgfoiwqhgertouiy23g5tiqu34hr"

export GROQ_API_KEY="fakeapikeyoweiuyrhgfoiwqhgertouiy23g5tiqu34hr"

export ANTHROPIC_API_KEY="fakeapikeyoweiuyrhgfoiwqhgertouiy23g5tiqu34hr"

export PERPLEXITY_API_KEY="fakeapikeyoweiuyrhgfoiwqhgertouiy23g5tiqu34hr"

If you only have some or just one of the keys, that's fine, it wont break if the other functions don't have there keys set - you just wont be able to use them.
