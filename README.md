import java.util.Arrays;
import java.util.Comparator;

public class KnapSack {

	public static class Item {

		private final String name;
		private final int value;
		private final int weight;

		public Item(final String name, final int value, final int weight) {
			this.weight = weight;
			this.value = value;
			this.name = name;
		}

		@Override
		public String toString() {
			return "Item [name=" + name + ", value=" + value + ", weight="
					+ weight + "]";
		}

	}

	private final Item[] items;
	private final int maxWeight;

	public KnapSack(final Item[] items, int maxWeight) {

		this.items = items; // shallow copy
		this.maxWeight = maxWeight;

		// sort by weight -- O(nlogn)
		Arrays.sort(this.items, new Comparator<Item>() {
			@Override
			public int compare(Item arg0, Item arg1) {
				if (arg0.weight > arg1.weight)
					return 1;
				else if (arg0.weight < arg1.weight)
					return -1;
				// difference must be zero (must it?)
				return 0;
			}
		});
	}

	/** array for memoization */
	private int[][] M;

	/**
	 * initialise the array. -1 is marker that we have not yet filled a slot in
	 * M
	 */
	private void initCalculation(final int maxWeight) {
		M = new int[this.items.length + 1][maxWeight + 1];

		// /*
		for (int i = 0; i < this.items.length + 1; i++) {
			for (int j = 1; j < maxWeight + 1; j++) {
				M[i][j] = -1;
			}
		}
	}

	/** calculate maximal value **/
	public int fillKnapsack() {
		initCalculation(maxWeight);
		return calculateOPTvalue(this.items.length, maxWeight);
	}

	private int calculateOPTvalue(int itemsLeft, int weightLeft) {

		// \todo Do away with initialisation?
		if (itemsLeft == 0)
			return 0; // no items left
		if (weightLeft <= 0)
			return 0; // no space left in knapsack

		// itemsLeft are now at least 1 and weightLeft is positive

		// if not yet memoised, calculate the value:
		if (M[itemsLeft][weightLeft] == -1) {

			final Item current = items[itemsLeft - 1];
			final int valueWithoutCurrent = calculateOPTvalue(itemsLeft - 1,
					weightLeft);

			// enough space left for current item at all?
			if (current.weight > weightLeft) {
				// no space
				M[itemsLeft][weightLeft] = valueWithoutCurrent;
			} else { // enough space left
				int valueWithCurrent = current.value
						+ calculateOPTvalue(itemsLeft - 1, weightLeft
								- current.weight);
				M[itemsLeft][weightLeft] = Math.max(valueWithCurrent,
						valueWithoutCurrent);
			}
			// */
			/*
			int valueWithCurrent = current.value
			+ calculateOPTvalue(itemsLeft - 1, weightLeft
					- current.weight);
			M[itemsLeft][weightLeft] = Math.max(valueWithCurrent,
					valueWithoutCurrent);
			// */
		}
		return M[itemsLeft][weightLeft];
	}

	@Override
	public String toString() {

		StringBuffer s = new StringBuffer();

		// Table Header:
		s.append("Weight:\t");
		for (int j = 0; j < maxWeight + 1; j++) {
			s.append(j + "\t");
		}
		s.append("\n");

		// Table Content:
		for (int i = 0; i < items.length + 1; i++) {
			s.append(i + " Items\t");
			for (int j = 0; j < maxWeight + 1; j++) {
				s.append(M[i][j] + "\t");
			}
			s.append("\n");
		}

		return s.toString();
	}

	private void postProcessing(final int itemsLeft, final int weightLeft) {

		if (itemsLeft == 0)
			return;
		if (weightLeft <= 0)
			return;

		Item current = items[itemsLeft - 1];

		if (M[itemsLeft][weightLeft] > M[itemsLeft - 1][weightLeft]) {
			System.out.println("Selected item: " + current);
			postProcessing(itemsLeft - 1, weightLeft - current.weight);
		} else
			postProcessing(itemsLeft - 1, weightLeft);

	}

	public void printSelectedItems() {
		postProcessing(items.length, maxWeight);
	}

	public static void main(String[] args) {

		final Item[] items = { new Item("1", 1, 1), new Item("2", 6, 2),
				new Item("3", 18, 5), new Item("4", 22, 6),
				new Item("5", 28, 7) };

		final int maxWeight = 11;
		final KnapSack knapSack = new KnapSack(items, maxWeight);

		final int maxValue = knapSack.fillKnapsack();

		System.out.print(knapSack);
		System.out.println("Maximal Value of knapsack with maximal Weight "
				+ maxWeight + ": " + maxValue);

		knapSack.printSelectedItems();

	}

}
