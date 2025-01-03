please provide me with detailed instructions on how to improve my code base. think step by step and return specific instructions with code blocks on what i should do

---------------------------Agent---------------------------

Based on the code context, here are detailed steps to improve your codebase:

1. **Implement a Config Management System**
```lua
-- Create a new file: lua/config.lua
local M = {}

M.default_config = {
    anthropic = {
        model = "claude-3-opus-20240229",
        api_key_name = "ANTHROPIC_API_KEY",
        max_tokens = 1024,
    },
    openai = {
        model = "gpt-4-turbo-preview",
        api_key_name = "OPENAI_API_KEY",
        temperature = 0.7,
        presence_penalty = 0,
        top_p = 1,
    },
    -- Add other provider configs
}

M.config = {}

function M.setup(user_config)
    M.config = vim.tbl_deep_extend("force", M.default_config, user_config or {})
end

return M
```

2. **Create a Message Manager for Chat History**
```lua
-- Create a new file: lua/message_manager.lua
local M = {}

M.conversations = {
    anthropic = {},
    openai = {},
    groq = {},
    perplexity = {},
    ollama = { context = {} }
}

function M.add_message(provider, message)
    table.insert(M.conversations[provider], message)
end

function M.reset_conversation(provider)
    M.conversations[provider] = {}
    if provider == "ollama" then
        M.conversations[provider].context = {}
    end
end

function M.get_conversation(provider)
    return M.conversations[provider]
end

return M
```

3. **Create Provider-specific Classes**
```lua
-- Create a new file: lua/providers/base_provider.lua
local BaseProvider = {}

function BaseProvider:new(opts)
    local instance = {
        name = opts.name,
        model = opts.model,
        api_key = opts.api_key,
        url = opts.url
    }
    setmetatable(instance, self)
    self.__index = self
    return instance
end

function BaseProvider:make_request_args()
    error("make_request_args must be implemented by provider")
end

function BaseProvider:handle_response()
    error("handle_response must be implemented by provider")
end

return BaseProvider
```

4. **Implement Error Handling System**
```lua
-- Create a new file: lua/error_handler.lua
local M = {}

M.error_types = {
    API_ERROR = "api_error",
    NETWORK_ERROR = "network_error",
    CONFIG_ERROR = "config_error"
}

function M.handle_error(error_type, message)
    vim.schedule(function()
        vim.notify(
            string.format("[LLM.nvim] %s: %s", error_type, message),
            vim.log.levels.ERROR
        )
    end)
end

return M
```

5. **Implement Response Streaming Handler**
```lua
-- Create a new file: lua/stream_handler.lua
local M = {}

function M.create_stream_handler(opts)
    return {
        buffer = "",
        on_chunk = opts.on_chunk,
        on_complete = opts.on_complete,
        
        handle = function(self, chunk)
            self.buffer = self.buffer .. chunk
            local lines = vim.split(self.buffer, "\n")
            
            for i = 1, #lines - 1 do
                self:on_chunk(lines[i])
            end
            
            self.buffer = lines[#lines]
        end,
        
        finish = function(self)
            if self.buffer ~= "" then
                self:on_chunk(self.buffer)
            end
            self:on_complete()
        end
    }
end

return M
```

6. **Refactor Main LLM Module**
- Move provider-specific code to separate modules
- Implement dependency injection pattern
- Use the new config and message management systems

7. **Add Unit Tests**
```lua
-- Create a new file: tests/llm_spec.lua

---------------------------User---------------------------

does lua allow you to have mixed data types in a table? like i want to have say
    price = {
    openai = {gpt_4o={name="gpt-4o", input=1, output=2},
}

---------------------------Agent---------------------------

Yes, Lua allows you to have mixed data types in a table. In Lua, tables are flexible data structures that can hold different types of data, including other tables, strings, numbers, functions, and more.

Your example shows a nested table structure where you have a table named `price`, and within it, a table for `openai` which contains another table for `gpt_4o`. This structure is perfectly valid in Lua. Here’s how it could look:

```lua
price = {
    openai = {
        gpt_4o = {
            name = "gpt-4o",
            input = 1,
            output = 2
        }
    }
}
```

You can access the elements of this table like so:

```lua
print(price.openai.gpt_4o.name)    -- Output: gpt-4o
print(price.openai.gpt_4o.input)   -- Output: 1
print(price.openai.gpt_4o.output)  -- Output: 2
```

This demonstrates that you can indeed have a table (`price`) containing mixed data types (in this case, a nested table structure with string and number values). You can continue to nest tables as needed, allowing for a rich data representation.

---------------------------User---------------------------

hello

---------------------------Agent---------------------------

Hello! How can I assist you today?

---------------------------User---------------------------

my name is chris

---------------------------Agent---------------------------

Nice to meet you, Chris! How can I help you today?

---------------------------User---------------------------

whats my name?

---------------------------Agent---------------------------

Your name is Chris! How can I assist you further?

---------------------------User---------------------------

hello my name is greg
