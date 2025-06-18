# chatgpt4o-project
ChatGPT-4o 延伸研究
# 📝 互動式小說寫作 App（GPT-4o）

這是一個使用 OpenAI GPT-4o 製作的互動式小說創作工具。  
使用者可透過網頁介面輸入小說情境、選擇風格與角色特質，快速生成風格化小說段落。

# 使用技術

- OpenAI GPT-4o API
- Python 3
- Streamlit
- dotenv（API 金鑰安全管理）

#功能特色

自訂情境描述：自由輸入你想描寫的情境
風格切換：支援模仿村上春樹、九把刀、輕小說等多種風格
角色設定欄位：輸入角色名稱與性格特質，提升描寫深度
即時生成小說段落：由 GPT-4o 回應風格化內容
一鍵下載為文字檔：可儲存為 `.txt` 檔案

# 專案結構
chatgpt4o-project/
├── app.py # 主程式（Streamlit App）
├── .env # 儲存 OPENAI_API_KEY（不上傳）
├── requirements.txt # 套件需求（可選）
├── README.md # 專案說明文件
└── analysis/ # 模型比較實驗、結果檔案等


# 📝 作者註解區
- 本 App 使用 GPT-4o 模型，透過自然語言生成小說風格段落。
- 使用者可自訂文風與角色個性，支援輸出保存文字內容。
- 開發工具：Python + Streamlit + OpenAI + dotenv

## 🖥️ 在本地終端機執行 App 的完整流程

本應用為基於 Python 與 Streamlit 架構的互動式小說生成工具，使用者可在本地終端機中執行所有操作，並透過瀏覽器進行圖形化互動。以下為整體執行與開發流程說明：

---

### 📌 操作說明：

使用者需在終端機中依序執行以下指令，完成專案初始化、依賴安裝、API 金鑰設定與 App 啟動，系統將自動於本地瀏覽器開啟操作介面（通常為 http://localhost:8501）。

---

```bash
# 1. 建立專案資料夾與主程式
mkdir chatgpt4o-project
cd chatgpt4o-project
touch app.py

# 2. 建立虛擬環境（建議）
python3 -m venv venv
source venv/bin/activate     # Windows 使用：venv\\Scripts\\activate

# 3. 安裝必要套件
pip install streamlit openai python-dotenv

# 4. 建立 .env 檔並填入金鑰
touch .env
nano .env
# ⬇ 在檔案內填入以下內容
# OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx

# 5. 執行主程式 App
streamlit run app.py



## 🧠 完整 app.py 程式碼

```python
import streamlit as st
from openai import OpenAI
from dotenv import load_dotenv
import os

load_dotenv()
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

st.title("📝 互動式小說寫作 App")
st.markdown("使用 GPT-4o 創作小說、模擬文風、編寫角色心理！")

user_prompt = st.text_area("請輸入小說情境描述：", "角色獨自走進深夜雨後的書店")

style = st.selectbox("選擇文風：", [
    "村上春樹風格", "九把刀風格", "輕小說風", "第一人稱內心獨白", "客觀敘述風格"
])

with st.expander("🔧 選擇角色設定（可選）"):
    character_name = st.text_input("角色名稱", "小藍")
    character_traits = st.text_area("角色個性 / 情感 / 特點", "安靜、感性、對細節敏感")
    extra_context = f"角色名為{character_name}，個性特徵為：{character_traits}。"

if st.button("✍️ 生成小說段落"):
    style_prompt = f"請用「{style}」的寫法，描寫以下情境：{user_prompt}。{extra_context}"
    with st.spinner("生成中，請稍候..."):
        try:
            response = client.chat.completions.create(
                model="gpt-4o",
                messages=[{"role": "user", "content": style_prompt}]
            )
            result = response.choices[0].message.content.strip()
            st.markdown("---")
            st.subheader("🖋️ 小說段落：")
            st.write(result)
            st.download_button("💾 下載段落為 .txt", data=result, file_name="novel_output.txt")
        except Exception as e:
            st.error(f"生成失敗：{e}")

