# Stock_Data_Analysis
주식 데이터 분석 프로그램

# 프로젝트명
주식 데이터 분석
## 📝 프로젝트 소개
주식 데이터의 가격 변동성 및 환율 정보

## 🛠 사용 기술
- python


## 📊 시스템 구조
[시스템 구조도 이미지]

## 🔍 주요 기능
1. 주식 가격 변동성 확인


## 🎯 성능 개선
- 

## 📚 API 문서
- 


import FinanceDataReader as fdr
import pandas as pd

"""
### 한국 주식 코드 가져오기
"""


class DataReader :
    def __init__(self):
        self.df = self._load_all_markets()

    def _load_market(self, market):
        try:
            return fdr.StockListing(market)
        except Exception as e:
            print (f"[ERROR] {market} 데이터 로드 실패: {e}")
            return pd.DataFrame()

    def _load_all_markets(self):
        markets = ['KRX', 'KOSPI', 'KOSDAQ', 'KONEX']
        all_data = [self._load_market(m) for m in markets]
        combined = pd.concat(all_data, ignore_index=True)
        return combined

    def get_code(self, name):
        result = self.df[self.df['Name'] == name]
        if not result.empty:
            return result.iloc[0]['Code']
        else:
            print(f"[INFO] '{name}' 종목을 찾을 수 없습니다.")
            return None

    """
    2025.04.29 ### 키워드명으로 종목찾기
    """
    def search(self, keyword):
        return self.df[self.df['Name'].str.contains(keyword)]

##### stock_code.py

import tkinter as tk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
from stock_code import DataReader
from stock_graph import StockGraphViewer
import FinanceDataReader as fdr
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm
"""
### 종목별 CODE 추출 방법
finder = DataReader()

code = finder.get_code('카카오뱅크') ### 카카오뱅크만 추출
code = finder.search('카카오')      ### 카카오 키워드로 관련된 종목 추출
print(f"카카오뱅크 코드 : {code}")
"""

#code = '323410'  ### 카카오뱅크 종목코드
#stock_name = '카카오뱅크'

#viewer = StockGraphViewer(code, name=stock_name)
#viewer.show_graph()

StockGraphViewer()

"""
### 파이참내부 팝업창으로 실행
plt.figure(figsize=(10,5))
plt.plot(df.index, df['Close'], label='Close Price', color='blue')
plt.title('카카오뱅크 주가 (종가)')
plt.xlabel('날짜')
plt.ylabel('가격')
plt.grid(True)
plt.legend()

plt.show()
"""


#### stock_analysis.py


import tkinter as tk
from tkinter import messagebox
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import FinanceDataReader as fdr
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm

class StockGraphViewer:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("주식 코드로 그래프 보기")
        self.root.geometry("900x600")

        """
        @ 한글 폰트 & 깨짐 방지
        """
        plt.rcParams['font.family'] = 'Malgun Gothic'
        plt.rcParams['axes.unicode_minus'] = False
        """
        """

        self.code_label = tk.Label(self.root, text="종목 코드 : ")
        self.code_label.pack(pady=(20,5))

        self.code_entry = tk.Entry(self.root, width=20, font=("Arial", 14))
        self.code_entry.pack()

        self.plot_button = tk.Button(self.root, text="그래프 보기", command=self.plot_stock)
        self.plot_button.pack(pady=10)

        self.canvas = None

        self.root.mainloop()

    def plot_stock(self):
        code = self.code_entry.get().strip()

        if not code:
            messagebox.showwarning("입력오류", "종목 코드를 입력하세요.")
            return
        try:
            df = fdr.DataReader(code)
            if df.empty:
                raise ValueError("데이터 없음")
        except Exception as e:
            messagebox.showerror("데이터 오류", f"데이터를 불러올 수 없습니다 : \n{e}")
            return
        if self.canvas:
            self.canvas.get_tk_widget().destroy()

        fig, ax = plt.subplots(figsize=(10, 4))
        ax.plot(df.index, df['Close'], color='blue')
        ax.set_title(f"{code} 종가")
        ax.set_xlabel('날짜')
        ax.set_ylabel('가격')
        ax.grid(True)

        self.canvas = FigureCanvasTkAgg(fig, master=self.root)
        self.canvas.draw()
        self.canvas.get_tk_widget().pack()

        self.root.mainloop()

#### stock_graph.py
