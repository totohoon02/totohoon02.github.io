```python
# main
import sys
import asyncio
import uvicorn

from fastapi import FastAPI
from _config.routeConfiurator import include_routers

app = FastAPI()

include_routers(app)

if __name__ == "__main__":
  # 윈도우 eventloop 정책
  if sys.platform == "win32":
    asyncio.set_event_loop_policy(asyncio.WindowsProactorEventLoopPolicy())
  
  uvicorn.run(app, host="0.0.0.0", port=8888)
```

```python
# Service
import json
from dotenv import load_dotenv
from langchain_mcp_adapters.client import MultiServerMCPClient
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent

from .inf.mcpService import McpService
from _config.configurator import Configurator

class McpServiceImpl(McpService):
	def __init__(self):
		load_dotenv()
		self.model = ChatOpenAI(model="gpt-4o-mini")
		self.agent = None
		
	async def initialize(self):
		"""비동기 초기화 작업을 수행합니다."""
		server_config = self.create_server_config()
		client = MultiServerMCPClient(server_config)
		tools = await client.get_tools()
		self.agent = create_react_agent(self.model, tools)

	async def chat(self, query: str):
		# agent가 초기화되지 않았으면 초기화 수행
		if self.agent is None:
			await self.initialize()
		
		response = await self.agent.ainvoke({"messages": query})
		return response['messages'][-1].content

	def load_mcp_config(self):
		"""현재 디렉토리의 MCP 설정 파일을 로드합니다."""
		try:
			with open("./mcp_server_config.json", "r") as f:
				return json.load(f)
		except Exception as e:
			print(f"설정 파일을 읽는 중 오류 발생: {str(e)}")
			return None

	def create_server_config(self):
		"""MCP 서버 설정을 생성합니다."""
		config = self.load_mcp_config()
		server_config = {}

		if config and "mcpServers" in config:
			for server_name, server_config_data in config["mcpServers"].items():
				# command가 있으면 stdio 방식
				if "command" in server_config_data:
					server_config[server_name] = {
						"command": server_config_data.get("command"),
						"args": server_config_data.get("args", []),
						"transport": "stdio",
					}
				# url이 있으면 sse 방식
				elif "url" in server_config_data:
					server_config[server_name] = {
						"url": server_config_data.get("url"),
						"transport": "sse",
					}

		return server_config
```
