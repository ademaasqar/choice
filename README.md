
def bayes(prior_obstacle, p_reading_if_obstacle, p_reading_if_clear):
    score_obstacle = prior_obstacle * p_reading_if_obstacle
    score_clear = (1.0 - prior_obstacle) * p_reading_if_clear
    denominator = score_obstacle + score_clear
    if denominator == 0:
        raise ValueError("The observation has zero probability.")
    return score_obstacle / denominator


print("Single BLOCKED:", round(bayes(0.30, 0.80, 0.20), 4))
print("Single CLEAR: ", round(bayes(0.30, 0.20, 0.80), 4))


print("\nD1: Different prior probabilities")

for prior in [0.10, 0.30, 0.50, 0.70]:
    result = bayes(prior, 0.80, 0.20)
    print(prior, "->", round(result, 4))



print("\nD2: False alarm increased")

original = bayes(0.30, 0.80, 0.20)
changed = bayes(0.30, 0.80, 0.40)

print("Original:", round(original, 4))
print("Changed: ", round(changed, 4))



observations = ["BLOCKED", "CLEAR", "BLOCKED", "BLOCKED"]

emission = {
    "BLOCKED": (0.80, 0.20),
    "CLEAR": (0.20, 0.80)
}

p_obstacle_after = 0.30

print("\nt reading predicted_O updated_O updated_C")

for t, reading in enumerate(observations, start=1):

    if t == 1:
        
        predicted_o = p_obstacle_after

    else:
        
        predicted_o = (
            0.70 * p_obstacle_after
            + 0.20 * (1.0 - p_obstacle_after)
        )

    
    likelihood_o, likelihood_c = emission[reading]

    
    p_obstacle_after = bayes(
        predicted_o,
        likelihood_o,
        likelihood_c
    )

   
    print(
        t,
        reading,
        f"{predicted_o:.4f}",
        f"{p_obstacle_after:.4f}",
        f"{1.0 - p_obstacle_after:.4f}"
    )
