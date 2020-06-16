# Решение задачи кластеризации

<!-- TOC -->

- [Решение задачи кластеризации](#решение-задачи-кластеризации)
	- [Алгоритмы](#алгоритмы)
		- [K-means](#k-means)
		- [ISODATA](#ISODATA)
		- [Иерархическая кластеризация](#иерархическая-кластеризация)
		- [По первому пришедшему](#по-первому-пришедшему)
	- [Вспомогательные интерфейсы](#вспомогательные-интерфейсы)
		- [Интерфейс задачи IClusteringData](#интерфейс-задачи-iclusteringdata)
		- [Интерфейс кластеризатора IClustering](#интерфейс-кластеризатора-iclustering)
		- [Результат кластеризации CClusteringResult](#результат-кластеризации-cclusteringresult)

<!-- /TOC -->

Библиотека **NeoML** предоставляет различные методы решения задачи кластеризации.

## Алгоритмы

### K-means

[Метод k-средних](kMeans.md) (*k-means*) — наиболее популярный метод кластеризации. Этот алгоритм относит каждый объект к тому кластеру, к центру которого объект ближе всего. Реализован классом `CKMeansClustering`.

### ISODATA

[Эвристический алгоритм](ISODATA.md) кластеризации основан на близости точек по геометрическому расстоянию. Результат работы алгоритма во многом зависит от заданных начальных параметров. Реализован классом `CIsoDataClustering`.

### Иерархическая кластеризация

В библиотеке реализована наивная версия восходящей [иерархической кластеризации](Hierarchical.md): изначально все элементы множества относятся к отдельным кластерам, а в процессе работы алгоритма происходит объединение кластеров, в результате чего получается итоговое разбиение. Реализован классом `CHierarchialClustering`.

### По первому пришедшему

[Простой алгоритм](FirstCome.md) кластеризации, создающий новый кластер для каждого нового вектора, если он достаточно удален от уже существующих кластеров. Реализован классом `CFirstComeClustering`.

## Вспомогательные интерфейсы

### Интерфейс задачи IClusteringData

Все алгоритмы принимают набор данных для кластеризации в виде указателя на объект, реализующий интерфейс `IClusteringData`:

```c++
class IClusteringData : public virtual IObject {
public:
	// Количество векторов в наборе данных.
	virtual int GetVectorCount() const = 0;

	// Количество признаков в наборе.
	virtual int GetFeaturesCount() const = 0;

	// Получить вектор из набора.
	virtual CSparseFloatVectorDesc GetVector( int index ) const = 0;

	// Получить все вектора набора.
	virtual CSparseFloatMatrixDesc GetMatrix() const = 0;

	// Получить вес вектора.
	virtual double GetVectorWeight( int index ) const = 0;
};
```

### Интерфейс кластеризатора IClustering

Все алгоритмы кластеризации реализуют интерфейс `IClustering`.

```c++
class IClustering {
public:
	virtual ~IClustering() {};

	// Выполнить разбиение выборки на кластеры.
	// Возвращает true, если удалось успешно разбить данные на кластеры с заданными параметрами.
	virtual bool Clusterize( IClusteringData* data, CClusteringResult& result ) = 0;
};
```

### Результат кластеризации CClusteringResult

Результат кластеризации описывается структурой `CClusteringResult`.

```c++
class NEOML_API CClusteringResult {
public:
	int ClusterCount;
	CArray<int> Data;
	CArray<CClusterCenter> Clusters;
};
```

- *ClusterCount* — количество кластеров в результате;
- *Data* — номера кластеров, в которые попали объекты из входных данных (кластеры нумеруются от `0` до `ClusterCount - 1`);
- *Clusters* — центры получившихся кластеров.