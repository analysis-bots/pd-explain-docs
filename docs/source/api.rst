.. _exp_dataframe:

=============================================
ExpDataFrame Class API
=============================================

Overview
--------

`ExpDataFrame` is a specialized DataFrame class that extends the functionality of the popular pandas DataFrame. It is designed to provide additional capabilities for explaining data operations applied to DataFrames, making it easier to understand and work with data transformations.

.. inheritance-diagram:: ExpDataFrame
   :parts: 1

Key Attributes
--------------

- `operation`: An object representing the data operation that led to the current state of the DataFrame.
- `explanation`: A high-level explanation of the data operation.
- `filter_items`: A list of column names that have been filtered or selected from the DataFrame.

Methods
-------

.. method:: explain(schema: dict = None, attributes: List = None, use_sampling: bool | None = None, sample_size: int | float = 5000, top_k: int = None, explainer: Literal['fedex', 'outlier', 'many_to_one', 'shapley']='fedex', target=None, dir=None, figs_in_row: int = 2, show_scores: bool = False, title: str = None, corr_TH: float = 0.7, consider='right', value=None, attr=None, ignore=[], labels=None, coverage_threshold: float = 0.7, max_explanation_length: int = 3, separation_threshold: float = 0.3, p_value: int = 1, explanation_form: Literal['conj', 'disj', 'conjunction', 'disjunction'] = 'conj', prune_if_too_many_labels: bool = True, max_labels: int = 10, pruning_method='largest', bin_numeric: bool = False, num_bins: int = 10, binning_method: str = 'quantile', label_name: str = 'label', explain_errors=True, error_explanation_threshold: float = 0.05, add_llm_explanation_reasoning: bool = False, min_commonness: float = 0.5, no_exception_penalty_weight=0.1, balance_factor: float = 1, filter_columns: List[str] | str = None, aggregations: List[Tuple[str, str]] = None, groupby_columns: List[List[str]] | List[str] = None, correlation_aggregation_method: Literal['avg', 'max', 'sum'] = 'avg', max_filter_columns: int = 3, max_aggregation_columns: int = 3, allow_multiple_aggregations: bool = False, allow_multiple_groupbys: bool = False, use_all_groupby_combinations: bool = False, do_not_visualize: bool = False, log_query: bool = False, display_mode: Literal['grid', 'carousel'] = 'grid', beautify: bool = False, beautify_max_fix_attempts: int = 10, silent_beautify: bool = True)

        Generate an explanation for the dataframe, using the selected explainer and based on the last operation performed.

        :param explainer: The explainer to use. Currently supported: 'fedex', 'many to one', 'shapley', 'outlier', 'metainsight'. Note that 'outlier' is only supported for series, not for dataframes. Please also note that the `metainsight` explainer is currently in beta. defaults to 'fedex'.
        :param attributes: All explainers. Which columns to consider in the explanation.
        :param use_sampling: All explainers. Whether or not to use sampling when generating an explanation. This can massively speed up the explanation generation process, but may result in less accurate explanations. We use sampling methods that we have empirically tested to only minimally affect the accuracy of the explanations. Defaults to None, in which case the value set in the global configuration is used (which defaults to True).
        :param sample_size: All explainers. The number of samples to use when use_sampling is True. Can be either an integer or a float. If it is an integer, that number of samples will be used. If it is a float between 0 and 1, it will be interpreted as a percentage of the total number of samples. Defaults to 5000, which is also the minimum value.
        :param schema: Fedex explainer. Result columns, can change columns name and ignored columns.
        :param top_k: Fedex explainer. Number of explanations.
        :param figs_in_row: Fedex explainer. Number of explanations figs in one row.
        :param show_scores: Fedex explainer. show scores on explanation.
        :param title: Fedex / outlier / shapley explainers. explanation title.
        :param corr_TH: Fedex explainer. Correlation threshold, above this threshold the columns are considered correlated.
        :param target: Outlier explainer. Target value for the outlier explainer
        :param dir: Outlier explainer. Direction for the outlier explainer. Can be either 'high' or 'low'.
        :param consider: Fedex explainer. Which side of a join to consider for the explanation. Can be either 'left' or 'right'.
        :param labels: Many to one explainer. Cluster / group labels. Can either be a series or a column name. If a column name is provided, the column must be present in the dataframe. If you wish to explain the groups of a groupby operation, leave this parameter as None while calling explain on the groupy result. The labels will be automatically extracted from the groupby operation.
        :param coverage_threshold: Many to one explainer. Minimum coverage threshold. Coverage is defined as the % of the data in the group that is explained by the explanation. Defaults to 0.7.
        :param max_explanation_length: Many to one explainer. Maximum explanation length permitted. Defaults to 3.
        :param separation_threshold: Many to one explainer. Maximum separation threshold. Separation error is defined as the % of the data in groups other than the one being explained that is explained by the explanation. Defaults to 0.3.
        :param p_value: Many to one explainer. A scaling factor for the maximum number of attributes that will be considered as candidates for the explanation. n_attr = max_explanation_length * p_value. Setting this to a higher value may result in a more accurate explanation, but will also increase the computation time. Defaults to 1.
        :param explanation_form: Many to one explainer. The form of the explanation. Can be either 'conj' or 'disj', for conjunction and disjunction respectively. Defaults to 'conj'.
        :param prune_if_too_many_labels: Many to one explainer. If True, the labels will be pruned if there are too many labels to consider. Defaults to True.
        :param max_labels: Many to one explainer. The maximum number of labels permitted. Above this number, the labels will be pruned if prune_if_too_many_labels is True. Defaults to 10.
        :param pruning_method: Many to one explainer. The method to use when selecting which labels to prune. Cab be 'largest' - where the k labels with the most values are kept, 'smallest', 'random', 'max_dist' - where the k labels with the highest distance between their means are kept, 'min_dist', 'max_silhouette' - where the k groups with the highest silhouette score are kept, or 'min_silhouette'. Defaults to 'largest'.
        :param bin_numeric: Many to one explainer. Whether or not to bin numeric labels, if there are more labels than the specified number of bins. Defaults to False.
        :param num_bins: Many to one explainer. The number of bins to use when binning numeric labels. Defaults to 10.
        :param binning_method: The method to use when binning numeric labels. Can be either 'quantile' or 'uniform'.
        :param label_name: Many to one explainer. How to call the labels column in the explanation, if binning was used and the labels column did not have a name. Defaults to 'label'.
        :param explain_errors: Many to one explainer. Whether or not to explain where the separation error originates from for each explanation. Defaults to True.
        :param error_explanation_threshold: Many to one explainer. The threshold for how much a group needs to contribute to the separation error to be included in the explanation. Groups that contribute less than this threshold will be aggregated into a single group. Defaults to 0.05.
        :param add_llm_explanation_reasoning: All explainers. Enables using a LLM to generate additional context explanations, explaining why the explanations found occur. Defaults to False. Requires setting an API key. See the documentation of LLM integrations for more information. Note that setting this to True will increase the computation time by a potentially large amount, entirely dependent on the LLM API response time. Also note that the output of the LLM is not guaranteed to be accurate, and may contain errors, so use with caution.
        :param min_commonness: MetaInsight explainer. Patterns must encompass at-least this percentage of the values to be considered a common pattern. Defaults to 0.5.
        :param no_exception_penalty_weight: MetaInsight explainer. The weight given to the penalty in the case no exceptions are found to a common pattern. Defaults to 0.1. Higher values will give more priority to common patterns with exceptions.
        :param balance_factor: MetaInsight explainer. The weight given to exceptions when computing the score of a common pattern. Defaults to 1 - same ratio for both common patterns and exceptions. Higher values will give more priority to common patterns without exceptions.
        :param filter_columns: MetaInsight explainer. The columns to filter on when mining for common patterns.
        :param max_filter_columns: MetaInsight explainer. The maximum number of filter columns to use when automatically selecting the filter columns. Defaults to 3.
        :param aggregations: MetaInsight explainer. The aggregations to use when mining for common patterns.
        :param max_aggregation_columns: MetaInsight explainer. The maximum number of aggregation columns to use when automatically selecting the aggregation columns. Defaults to 3.
        :param groupby_columns: MetaInsight explainer. The columns to group by when mining for common patterns. If not provided, will be inferred automatically from the filter / aggregation columns.
        :correlation_aggregation_method: MetaInsight explainer. When auto-selecting groupby / filter / aggregation columns, a correlation based method is used to determine the best method to use. This parameter determines which aggregation function is used to aggregate the computed correlation in the case of multiple columns. Can be either 'avg', 'max' or 'sum'.
        :param allow_multiple_aggregations: MetaInsight explainer. Whether or not to allow multiple aggregations to be used in the same pattern. Defaults to False. May result in more complex and less interpretable patterns if set to True.
        :param allow_multiple_groupbys: MetaInsight explainer. Whether or not to allow multiple groupbys to be used in the same pattern. Defaults to False. May result in more complex and less interpretable patterns, possibly with multiple (almost or completely) disjoint indexes if set to True.
        :param use_all_groupby_combinations: MetaInsight explainer. When automatically inferring on a result of a groupby operation, whether to use all combinations of the groupby columns or just the provided ones. For example, if set to True and the groupby columns are ['A', 'B'], the groupby columns will be [['A'], ['B'], ['A', 'B']]. If set to False, only the provided groupby columns will be used.
        :param do_not_visualize: If True, the explanation will not be visualized (if the explainer supports disabling visualization).
        :param display_mode: Fedex explainer and MetaInsight explainer. How to visualize the multiple figures returned by the explainer. Can be either 'grid' for a regular plot in a grid format displaying all plots at once, or 'carousel' for a carousel that shows one figure at a time with navigation buttons.
        :param beautify: MetaInsight and Fedex explainers. If True, we will attempt to beautify the explanation by having a LLM generate code for producing a more visually appealing explanation for this specific case. Defaults to False. Please note that: 1. This will increase the computation time by a potentially large amount, entirely dependent on the LLM API response time. 2. The output of the LLM is not guaranteed to be accurate, and may contain errors, so use with caution.
        :param beautify_max_fix_attempts: MetaInsight and Fedex explainers. The maximum number of attempts to fix the returned code from the LLM to either make it work or improve the visualization, if the beautify parameter is set to True. Defaults to 10.
        :param silent_beautify: MetaInsight and Fedex explainers. If True, the beautify process will not print any information about its progress, and will only return the final result. Defaults to False.

        :return: A visualization of the explanation, if possible. Otherwise, the raw explanation.

.. method:: present_deleted_correlated(figs_in_row=2)

    Generate explanations for features that were removed due to high correlation with other features.

    :param figs_in_row: The number of explanation figures to display in one row.
    :type figs_in_row: int, optional
    :return: Explanation Figures

.. method:: copy(deep=True)

    Create a copy of the `ExpDataFrame`.

    :param deep: If `True`, make a deep copy, including data and indices.
    :type deep: bool, optional
    :return: A copy of the `ExpDataFrame`.
