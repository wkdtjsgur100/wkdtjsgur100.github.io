---
layout: post
section-type: post
title: 다단계 그래프(Multistage Graph) 구현
category: algorithm
tags: [ 'algorithm' ]
comments: true
---

``` java
import java.io.BufferedReader;
import java.io.FileReader;
import java.util.Scanner;

public class MultistageGraph {
	private static Scanner scanner = new Scanner(System.in); // for input
	private static int[][] graph; // 인접행렬로 표현한 그래프
	private static int[][] d; // 최소 비용 경로
	private static int numOfVertices, numOfEdges; // 정점 개수, 간선 개수

	// 최소 비용 및 경로 구하기, i는 시작 구간 index, j는 정점 번호  
	public static int cost(int i, int j)
	{
		int min = 999999999;
		int minIndex = -1;

		for(int l=0;l<numOfVertices;l++){
			if(graph[j][l] != 0) { // 그래프가 연결되어 있는 경우에만 계산
				int c = graph[j][l] + cost(i+1, l);
				if(min > c) { // 제일 작은 cost를 min에 넣는다
					min = c;
					minIndex = l;
				}
			}
		}

		if(minIndex == -1) // 연결된 노드가 하나도 없는 경우.
			return 0;
		else {
			d[i][j] = minIndex; // 제일 작은 cost로 선택된 간선의 번호를 저장한다.
			return min;
		}
	}
	// 경로 출력
	public static void printRoute(int i, int j)
	{
		System.out.print((j+1)+ " ");
		if(d[i][j] != 0)
			printRoute(i+1, d[i][j]);
	}
	// 인접 행렬 출력
	public static void printGraphMatrix()
	{
		for(int i=0;i<graph.length;i++){
			for(int j=0;j<graph[i].length;j++)
				System.out.print(graph[i][j]+" ");
			System.out.println("");
		}
	}
	public static void inputGraphMatrix()
	{
		/*
		// 사용자 입력용
		for(int i=0;i<numOfEdges;i++) {
			System.out.print(i + "번째 간선: ");
			int source = scanner.nextInt();
			int destination = scanner.nextInt();
			int weight = scanner.nextInt();

			// 인접 행렬에 weight 추가. weight > 0
			graph[source-1][destination-1] = weight;  
		}
		*/
		// 파일 입력 용
		try {
			BufferedReader in = new BufferedReader(new FileReader("src/graph.txt"));

			String line = null;

			while(true){
				line = in.readLine();
				if(line == null) break;
				String[] items = line.split(" ");

				int source = Integer.parseInt(items[0]);
				int destination = Integer.parseInt(items[1]);
				int weight = Integer.parseInt(items[2]);

				graph[source-1][destination-1] = weight;
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	public static void main(String[] args)
	{
		System.out.print("정점 수와 간선 수를 입력: ");
		numOfVertices = scanner.nextInt();
		numOfEdges = scanner.nextInt();

		graph = new int[numOfVertices][numOfVertices];
		d = new int[numOfVertices][numOfVertices];

		inputGraphMatrix();

		System.out.println("인접행렬 출력 ============== ");
		printGraphMatrix();

		System.out.println("최소 비용: " + cost(0,0));

		System.out.println("최소 비용 경로 출력 ===========");
		printRoute(0,0);
	}
}

```
